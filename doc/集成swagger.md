#### 集成步骤
* 在springBoot项目或者SpringCloud服务启动的那个项目中添加Swagger2的依赖(现在最新版是swagger3)

        <dependency>
          <groupId>io.springfox</groupId>
          <artifactId>springfox-swagger2</artifactId>
          <version>2.9.2</version>
      </dependency>
      <dependency>
          <groupId>io.springfox</groupId>
          <artifactId>springfox-swagger-ui</artifactId>
          <version>2.9.2</version>
      </dependency>
* 编写swagger2的配置文件，注意：配置文件和springboot服务启动的文件放在同一个位置或者放在Controller层的上级目录。
    
      @Configuration
      @EnableSwagger2
      public class Swagger2 {
          @Bean
          public Docket createRestApi() {
              return new Docket(DocumentationType.SWAGGER_2)
                      .apiInfo(apiInfo())
                      .select()
                      .apis(RequestHandlerSelectors.basePackage("com.shareniu.web"))
                      .paths(PathSelectors.any())
                      .build();
          }

          private ApiInfo apiInfo() {
              return new ApiInfoBuilder()
                      .title("跟着分享牛学习Springboot源码分析系列课程")
                      .description("更多Spring Boot相关文章请关注分享牛的博客")
                      .termsOfServiceUrl("http://www.shareniu.com/")
                      .contact("牛牛")
                      .license("Copyright 2017-2018 分享牛")
                      .version("1.0")
                      .build();
          }
         }
* 服务启动类加上@EnableSwagger2注解，开启swagger功能，然后服务启动成功之后，访问：http://localhost:8080/swagger-ui.html,Springcloud：http://localhost:8000/swagger-ui.html
#### Swagger的原理
