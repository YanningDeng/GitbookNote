# 设计自定义Taco

### 需求

实现用户可以从丰富的配料表中自定义Taco的功能。



### 对象

玉米饼配料：属性包括id，名称，类型。类型里可以选择蛋白质，奶酪，酱汁等。

玉米饼：属性包括名称和配料。

订单：属性包括名称，街道，城市，州，zip等。



### SpringMVC架构

##### 控制器

主要工作是处理 HTTP 请求，或者将请求传递给视图以呈现 HTML（浏览器显示），或者直接将数据写入响应体（RESTful）。大多数请求处理方法通过返回视图的**逻辑名称**来结束。

- 视图控制器：只将请求转发给视图的控制器。

  ```java
  package tacos.web;
  
  import org.springframework.context.annotation.Configuration;
  import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
  import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
  
  @Configuration
  public class WebConfig implements WebMvcConfigurer {
      
      @Override
      public void addViewControllers(ViewControllerRegistry registry) {
          registry.addViewController("/").setViewName("home");
      }
  }
  ```

  `WebMvcConfigurer `接口定义了几个配置 Spring MVC 的方法。尽管它是一个接口，但它提供了所有方法的默认实现，因此只需覆盖所需的方法。这里也可以不用新建一个配置类实现该逻辑，任何实现了`WebMvcConfigurer `接口的配置类都可以实现并覆盖`addViewControllers`方法。

这里我们需要的控制器类包括：订单控制器，设计控制器。

##### 视图

我们需要订单，设计和主页视图。

##### 模型

模型是一个对象，它在控制器和负责呈现数据的视图之间传输数据。我们需要的模型也就是上面列出的对象。



### Spring表单验证

Spring 支持 Java's Bean Validation API（也称为 JSR-303；https://jcp.org/en/jsr/detail?id=303）。这使得声明验证规则比在应用程序代码中显式地编写声明逻辑更容易。**使用 Spring Boot，不需要做任何特殊的事情来将验证库添加到项目中**，因为 Validation API 和 Validation API 的 Hibernate 实现作为Spring Boot web 启动程序的临时依赖项自动添加到了项目中。

如果出现没有添加依赖就报错的情况，需要检查pom.xml中<parent>的版本：

```java
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.4.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

**在 Spring MVC 中应用验证，需要**：

- 对要验证的类声明验证规则。如：

  ```java
  package tacos;
  
  import java.util.List;
  import javax.validation.constraints.NotNull;
  import javax.validation.constraints.Size;
  import lombok.Data;
  
  @Data
  public class Taco {
      
      @NotNull
      @Size(min=5, message="Name must be at least 5 characters long")
      private String name;
      
      @Size(min=1, message="You must choose at least 1 ingredient")
      private List<String> ingredients;
  }
  ```

- 指定验证应该在需要验证的控制器方法中执行。

  ```java
  @PostMapping
  public String processOrder(@Valid Order order, Errors errors) {
      if (errors.hasErrors()) {
          return "orderForm";
      }
      
      log.info("Order submitted: " + order);
      return "redirect:/";
  }
  ```

- 修改表单视图以显示验证错误。

  ```java
  <label for="ccNumber">Credit Card #: </label>
  <input type="text" th:field="*{ccNumber}"/>
  <span class="validationError"
        th:if="${#fields.hasErrors('ccNumber')}"
        th:errors="*{ccNumber}">CC Num Error</span>
  ```

  

### 缓存模板问题

默认情况下，模板在第一次使用时只解析一次，解析的结果被缓存以供后续使用。对于生产环境来说，这是一个很好的特性，因为它可以防止对每个请求进行冗余的模板解析，从而提高性能。

但是，在开发时，这个特性并不那么好。假设启动了应用程序并点击了玉米饼设计页面，并决定对其进行一些更改。当刷新 web 浏览器时，仍然会显示原始版本。查看更改的惟一方法是重新启动应用程序，这非常不方便。

**只需将模板的缓存使能属性设置为 false，就可以禁用缓存。**默认情况下，缓存使能属性值为true。

`Thymeleaf`的缓存使能属性为：`spring.thymeleaf.cache`。禁用缓存属性只需要在`application.properties`文件中添加如下行代码：

```java
spring.thymeleaf.cache = false
```

