## Spring注释

+ `@SpringBootApplication`：该类是一个 Spring 引导应用程序。`@SpringBootApplication`是一个组合了`@SpringBootConfiguration`，`@EnableAutoConfiguration`和`@ComponentScan`三个注释的复合应用程序。

+ `@SpringBootConfiguration`：指定这个类为配置类。这个注释是`@Configuration` 注释的一种特殊形式。

+ `@EnableAutoConfiguration`： 启用 Spring 自动配置。

- `@ComponentScan`：启用组件扫描。这允许你声明其他带有 `@Component`、`@Controller`、`@Service` 等注释的类，以便让 Spring 自动发现它们并将它们注册为 Spring 应用程序上下文中的组件。
- `@RunWith`：是一个 JUnit 注释，提供了一个测试运行器来引导 JUnit 运行测试用例。
- `@SpringBootTest`：告诉 JUnit 使用 Spring 引导功能引导测试。
- `@Controller`：此注释用于将该类标识为控制器并将其标记为组件扫描的候选对象，以便 Spring 将发现该类并在 Spring 应用程序上下文中自动创建该类的实例作为 bean。
- `@GetMapping`：指示如果接收到根路径为参数值的HTTP GET 请求，则此方法应该处理该请求。
- `@WebMvcTest`：是 Spring Boot 提供的一个特殊测试注释，它安排测试在 Spring MVC 应用程序的上下文中运行。为测试 Spring MVC 提供了 Spring 支持。
- `@Data`：由 Lombok 库提供，它告诉 Lombok 生成所有缺少的方法，以及接受所有最终属性作为参数的构造函数。**通过使用 Lombok，可以让 `Ingredient` 的代码保持整洁。**这里注意，使用LomBook库，需要将其作为依赖添加到项目中。
- `@Slf4j`： Lombok 提供的注释，在运行时将自动生成类中的 SLF4J（Java 的简单日志门面，https://www.slf4j.org/）记录器。
- `@RequestMapping`：注释在类级应用时，指定该控制器处理的请求的类型。
- `@GetMapping `：`@GetMapping` 与类级别的` @RequestMapping` 配对使用，指定何时接收`@RequestMapping`参数中的地址的 HTTP **GET** 请求。`@GetMapping`注释的方法将用来处理该请求。
- `@PostMapping`：处理 HTTP POST 请求。
- `@PutMapping`：处理 HTTP PUT 请求。
- `@DeleteMapping`：处理 HTTP DELETE 请求。
- `@PatchMapping`：处理 HTTP PATCH 请求。
- `@CreditCardNumber `：该注释声明属性的值必须是通过 Luhn 算法（https://en.wikipedia.org/wiki/Luhn_algorithm）检查过的有效信用卡号。
- `@Valid`：注释在控制器方法中的参数前，表示Spring MVC 在提交的参数对象绑定到提交的表单数据之后，以及调用该控制器方法之前，对提交的参数对象执行验证。

