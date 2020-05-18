## Spring项目初始化

使用Intellij初始化一个Spring项目。依据Spring in Action 5th edition中的附录内容进行操作。

### pom.xml

pom.xml是maven项目的构建规范，具体如下所示：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.0.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>sia</groupId>
    <artifactId>taco-cloud</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>taco-cloud</name>
    <description>Taco Cloud Example</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```

这里我的xml文件中没有`<packaging>`标签，是因为Spring项目默认了将应用程序构建为可执行的JAR文件。虽然 WAR 文件非常适合部署到传统的 Java 应用服务器，但是它们并不适合大多数云平台。尽管一些云平台（如 Cloud Foundry）能够部署和运行 WAR 文件，但是所有的 Java 云平台都能够运行可执行的 JAR 文件。如果有相应的需求，就将`<packaging>`对应的值设置为WAR即可。

`<artifactId>`指的是项目的实际具体名称。这里可以看到依赖中很多依赖项的artifactId中都包含starter。Spring Boot starter 依赖项的特殊之处在于，它们本身通常没有任何库代码，而是间接地引入其他库。这些 starter 依赖提供了三个主要的好处：

+ 构建的文件将会小得多，也更容易管理，因为不需要对每一个可能需要的库都声明一个依赖项。

+ 可以根据它们提供的功能来考虑需要的依赖关系，而不是根据库名来考虑。如果正在开发一个 web 应用程序，那么将添加 web starter 依赖项，而不是一个编写 web 应用程序的各个库的清单。

+ 不用担心 library 版本问题。可以相信的是，对于给定版本的 Spring Boot，可间接地引入的库的版本将是兼容的，只需要考虑使用的是哪个版本的 Spring Boot。

最后是设置项目构建中需要的插件。这里的插件是`spring-boot-maven-plugin`。这个插件的作用是：

+ 提供了一个 Maven 编译目标，使得项目能够使用 Maven 运行应用程序。

+ 确保所有的依赖库都包含在可执行的 JAR 文件中，并且在运行时类路径中可用。

+ 在 JAR 文件中生成一个 manifest 文件，表示引导类（在本项目中是 `TacoCloudApplication`）是可执行 JAR 的主类。

  

### 引导类`TacoCloudApplication`

引导类`TacoCloudApplication`内容如下：

```java
@SpringBootApplication
public class TacoCloudApplication {

    public static void main(String[] args) {
        SpringApplication.run(TacoCloudApplication.class, args);
    }

}
```

其中，最重要的是调用了静态类SpringApplication的`run()`方法，这个方法执行应用程序的实际引导，创建Spring 应用程序上下文。传递给 `run()` 方法的两个参数是一个配置类和命令行参数。



### DevTools 

Devtools拥有很多功能，包括当代码更改时自动重启应用程序，当以浏览器为目标的资源（如模板、JavaScript、样式表等）发生变化时，浏览器会自动刷新，自动禁用模板缓存等。其中自动重启应用程序功能原理如下：

​	当 DevTools 起作用时，**应用程序被加载到 Java 虚拟机（JVM）中的两个单独的类加载器中**。一个类装入器装入 Java 代码、属性文件以及项目的 src/main/path 中的几乎所有东西。这些项目可能会频繁更改。另一个类加载器加载了依赖库，它们不太可能经常更改。

​	当检测到更改时，DevTools 只重新加载包含项目代码的类加载器，并重新启动 Spring 应用程序上下文，但不影响其他类加载器和 JVM。尽管这一策略很微妙，但它可以略微减少启动应用程序所需的时间。

​	这种策略的缺点是**对依赖项的更改在自动重新启动时不可用**。这是因为类装入器包含依赖项库 不是自动重新加载。这意味着，每当在构建规范中添加、更改或删除依赖项时，都需要重新启动应用程序才能使这些更改生效。



### 总结

Spring 的目标是让开发人员轻松应对挑战，比如创建 web 应用程序、使用数据库、保护应用程序和使用微服务。

Spring Boot 构建在 Spring 之上，简化了依赖管理、自动配置和运行时监控，让 Spring 变得更加简单。



