#### 概述
###### SpringBoot是由Pivotal团队提供，继Spring、Springmvc框架之后的一个全新框架，默认配置了很多框架的使用方式，就像Maven整合了所有的Jar包，SpringBoot整合了所有的框架。能够简化配置文件，快速构建web应用，内置tomcat，无需打包部署，直接运行。使用方便、快速搭建项目，大大节省工作量。

    1）配置 web.xml，加载 Spring 和 Spring mvc
    2）配置数据库连接、配置 Spring 事务
    3）配置加载配置文件的读取，开启注解
    4）配置日志文件
      .....
    
    配置完成之后部署Tomcat调试，springboot的引入将让它成为过去式。
###### SpringBoot开发具体的一个个微服务，SpringCloud在提供统一管理、协调各个微服务。为微服务提供各个服务的注册中心、网关、配置中心、负载均衡、熔断机制、服务降级、服务监控、服务细节屏蔽等等。
#### 集成步骤
##### 添加依赖
            <!-- 继承SpringBoot官方指定的父工程 -->	
            <parent>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-parent</artifactId>
                <version>1.5.8.RELEASE</version>
            </parent>

            <dependencies>
                <!-- 启动器 -->
                <dependency>
                    <!-- 指定groupId和artifactId即可，版本已在父工程中定义 -->
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-web</artifactId>
                </dependency>
            </dependencies>
             
             spring-boot-starter：核心模块，包括自动配置支持、日志和YAML；
             spring-boot-starter-test：测试模块，包括JUnit、Hamcrest、Mockito。
             spring-boot-starter-web默认为我们提供一些SpringMVC必要的组件：

             必要的ViewResolver，比如ContentNegotiatingViewResolver和BeanNameViewResolver。
             将必要的Converter，GenericConverter和Formatter等bean注册到IOC容器。
             添加一系列的HttpMessageConverter以便支持对web请求和相应的类型转换。自动配置MessageCodesResolver。
             如果默认的SpringMVC组件并不能满足我们的需求，都可以在IOC容器中注册新的同类型的bean来替换，
             或者直接提供一个基于WebMvcConfigurerAdapter类型的ben来定制，甚至直接提供一个标注了 
             @EnableWebMvc的@configuration配置类完全接管所有SpringMVC的相关配置，自己完全重新配置。
             spring-boot-starter-web默认使用嵌入式的tomcat作为web容器对外提供HTTP服务。
             spring-boot-starter-web提供了很多以server.为前缀的配置项用于对嵌入式Web容器提供配置，比如：
             server.port,server.address,server.ssl.*,server.tomcat.*

##### 创建主启动类

            @SpringBootApplication 
            @ComponentScan("com.*")
            public class SpringBootStudyConfigApplication
                public static void main(String[] args) {
                    SpringApplication.run(SpringBootStudyConfigApplication.class, args);
                }
            }
            @SpringBootApplication：表示这是一个springboot应用，实现自动配置功能。
            SpringBootApplication由3个注解组成，分别是：
            @Configuration：标注IoC容器的配置类
            @EnableAutoConfiguration：把所有标注@Configuration的都加载到当前SpringBoot创建并使用的IoC容器中
            @ComponentScan：默认会扫描当前package下的所有加了@Component 、@Repository、@Service、@Controller的类到IoC容器中
            
            @ComponentScan("com.*"):SpringBoot中主启动类会自动扫描该类所在包的子包，相当于xml配置文件中的<mvc:context scan=”包扫描路径”/>
            注意：要进行扫描的包必须处于主启动类的子包中
        
##### 编写Controller
                
            @RestController
            public class HelloWorldController {
                @RequestMapping("/hello")
                public String index() {
                    return "Hello World";
                }
            }
            @RestController的意思就是controller里面的方法都以json格式输出
##### 启动主程序，打开浏览器访问：http://localhost:8080/hello
##### 自动装配的实现原理
###### spring中使用redis需要在xml定义bean,现在只需要依赖一个spring-boot-starter-data-redis的jar包,jar中定义了RedisConfiguration,当启动的时候spring会自动装载RedisConfiguration,那spring是如何知道配置类在哪里的呢?RedisConfiguration类的路径放在了classpath*META-INF/spring.factories的文件中,spring会加载这个文件中配置的configuration

        SpringApplication.run(AppConfig.class,args);
        执行中refreshContext(context)内部会解析我们的配置类上的标签.
        实现自动装配功能的注解@EnableAutoConfiguration
        @EnableAutoConfiguration注解里面的@Import引入的配置类.AutoConfigurationImportSelector，
        AutoConfigurationImportSelector类中方法：
        SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),getBeanClassLoader());
        SpringFactoriesLoader.loadFactoryNames的作用就是读取jar包中
        项目的META-INF/spring.factories文件.spring.factories配置了要自动装配的Configuration类。
