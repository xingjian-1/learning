#### 启动类注解
###### @SpringBootApplication

        SpringBootApplication由3个注解组成，分别是：
        @Configuration：标注IoC容器的配置类
        @EnableAutoConfiguration：把所有标注@Configuration的都加载到当前SpringBoot创建并使用的IoC容器中
        @ComponentScan：默认会扫描当前package下的所有加了@Component 、@Repository、@Service、@Controller的类到IoC容器中
###### 自动装配的实现原理
###### 如果是之前的spring中使用redis需要在xml定义bean,现在只需要依赖一个spring-boot-starter-data-redis的jar包,jar中定义了RedisConfiguration,当启动的时候spring会自动装载RedisConfiguration,那spring是如何知道配置类在哪里的呢?RedisConfiguration类的路径放在了classpath*META-INF/spring.factories的文件中,spring会加载这个文件中配置的configuration

        SpringApplication.run(AppConfig.class,args);执行中refreshContext(context)内部会解析我们的配置类上的标签.实现自动装配功能的注解@EnableAutoConfiguration
        @EnableAutoConfiguration注解里面的@Import引入的配置类.AutoConfigurationImportSelector，
        AutoConfigurationImportSelector类中方法：SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),getBeanClassLoader());
        SpringFactoriesLoader.loadFactoryNames的作用就是读取jar包中的/项目中的META-INF/spring.factories文件.spring.factories配置了要自动装配的Configuration类。

