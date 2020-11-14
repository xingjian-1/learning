#### 概述
###### SpringBoot是由Pivotal团队提供，继Spring、Springmvc框架之后的一个全新框架，默认配置了很多框架的使用方式，就像Maven整合了所有的Jar包，SpringBoot整合了所有的框架。能够简化配置文件，快速构建web应用，内置tomcat，无需打包部署，直接运行。
###### 解决的问题
###### 非常方便、快速搭建项目，大大节省工作量，如下：

    1）配置 web.xml，加载 Spring 和 Spring mvc
    2）配置数据库连接、配置 Spring 事务
    3）配置加载配置文件的读取，开启注解
    4）配置日志文件
    ...
    配置完成之后部署Tomcat调试 哪怕是一个小的功能可能都需要，这样操作一遍，实在麻烦！！！
    springboot的引入将让它成为过去式
###### 添加依赖

            <!-- 继承SpringBoot官方指定的父工程 -->	
            <parent>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-parent</artifactId>
                <version>1.5.8.RELEASE</version>
            </parent>

            <dependencies>
                <!-- 加入Web开发所需要的场景启动器 -->
                <dependency>
                    <!-- 指定groupId和artifactId即可，版本已在父工程中定义 -->
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-web</artifactId>
                </dependency>
            </dependencies>
             
             spring-boot-starter：核心模块，包括自动配置支持、日志和YAML；
             spring-boot-starter-test：测试模块，包括JUnit、Hamcrest、Mockito。
###### 创建主启动类

            @SpringBootApplication 
            @ComponentScan("com.*")
            public class SpringBootStudyConfigApplication
                public static void main(String[] args) {
                    SpringApplication.run(SpringBootStudyConfigApplication.class, args);
                }
            }
            @SpringBootApplication：表示这是一个springboot应用，实现自动配置功能。
            @ComponentScan("com.*"):SpringBoot中主启动类会自动扫描该类所在包的子包，相当于xml配置文件中的<mvc:context scan=”包扫描路径”/>
            注意：要进行扫描的包必须处于主启动类的子包中
###### 编写Controller内容
                
             @RestController
            public class HelloWorldController {
                @RequestMapping("/hello")
                public String index() {
                    return "Hello World";
                }
            }
            @RestController的意思就是controller里面的方法都以json格式输出
###### 启动主程序，打开浏览器访问http://localhost:8080/hello

