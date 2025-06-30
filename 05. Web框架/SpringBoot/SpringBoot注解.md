## SpringBoot 注解总结

> @RestController 注解
>
> 1. @RestController 注解相当于@Controller + @ResponseBody
> 2. @Controller 注解用于表示控制器
> 3. @ResponseBody 注解的类中的由@RequestMapping注解的方法，返回值转换为 json 发送给 http 的响应体中，不经过视图解析器
> 4. @RestController 是 REST 风格的注解

> @SpringBootApplication 注解
>
> 1. @SpringBootApplication 注解用于表示 SpringBoot 的 运行类
> 2. @SpringBootApplication
>    等同于
>    @SpringBootConfiguration (继承 @Configuration注解：将该类标识为配置类并加入容器)
>    @EnableAutoConfiguration(找到jar包，进行自动配置)
>    @ComponentScan("com.chr.boot")(用于设置组件扫描的位置)

> @Autowired 注解：自动注入属性

> @PathVariable 注解 ：获取参数，在 REST 风格中，获取 url 中通过占位符传递的参数

> @RequestMapping 注解：通过 url 来执行对应的方法

> @ImportResource 注解：用于将xml配置文件注入到容器中
>
> 例：@ImportResource("classpath:beans.xml")

> @Import 注解：将类导入，给容器创建出组件，默认组件名为全类名
>
> 例：@Import({one.class})

> ==@Component==：用于普通创建对象
>
> ==@Service==：用于 service 层
>
> ==@Controller==用于 web 层
>
> ==@Repository==：用于 dao 层

> @Conditional 注解：条件绑定，有许多子注解，常用子注解

> @ConfigurationProperties（prefix = "前缀"）注解可以将配置信息自动导入到<font color="red">已经在容器中的 Bean</font>

>@EnableConfigurationProperties（类名.class）在<font color="red">配置类上写</font>，指明开启属性绑定并且将该类加入容器中
>
>@ConfigurationProperties 在类上写

> @RequestMapping
>
> @DeleteMapping
>
> @PutMapping
>
> @GetMapping
>
> @PostMapping
>
> 写在方法上，请求路径会映射到该方法，下面四个是对第一个的一层封装，method进行设置

> @MapperScan("com.atguigu.admin.mapper") 查找对应包下的mapper接口，接口上不用加@Mapper注解

> @Validated注解：搭配
>
> @NotBlank(message = "手机号不能为空")
> @Pattern(regexp = PHONE_REGEXP, message = "手机号格式不正确")
>
> 注解用，对于DTO而言，可以在参数列表中，对于@RequestParam接收的参数，需要放在控制器类上面
