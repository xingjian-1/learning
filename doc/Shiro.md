#### 简介
Shiro是Apache下的一个开源项目。shiro属于轻量级框架，相对于SpringSecurity简单的多，也没有SpringSecurity那么复杂。

          1. Authentication：身份认证/登录(账号密码验证)。
          2. Authorization：授权，即角色或者权限验证。
          3. Session Manager：会话管理，用户登录后的session相关管理。
          4. Cryptography：加密，密码加密等。
          5. Web Support：Web支持，集成Web环境。
          6. Caching：缓存，用户信息、角色、权限等缓存到如redis等缓存中。
          7. Concurrency：多线程并发验证，在一个线程中开启另一个线程，可以把权限自动传播过去。
          8. Testing：测试支持；
          9. Run As：允许一个用户假装为另一个用户（如果他们允许）的身份进行访问。
          10. Remember Me：记住我，登录后，下次再来的话不用登录了。
          
#### 集成

              <?xml version="1.0" encoding="UTF-8"?>
              <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                       xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
                  <modelVersion>4.0.0</modelVersion>
                  <parent>
                      <groupId>org.springframework.boot</groupId>
                      <artifactId>spring-boot-starter-parent</artifactId>
                      <version>2.3.4.RELEASE</version>
                      <relativePath/>
                  </parent>
                  <groupId>com.wsl</groupId>
                  <artifactId>spring-shiro-demo</artifactId>
                  <version>0.0.1-SNAPSHOT</version>
                  <name>spring-shiro-demo</name>
                  <description>Demo project for Spring Boot</description>

                  <properties>
                      <java.version>1.8</java.version>
                      <spring.shiro.version>1.6.0</spring.shiro.version>
                  </properties>

                  <dependencies>
                      <dependency>
                          <groupId>org.springframework.boot</groupId>
                          <artifactId>spring-boot-starter-web</artifactId>
                      </dependency>
                      <dependency>
                          <groupId>org.springframework.boot</groupId>
                          <artifactId>spring-boot-starter-test</artifactId>
                          <scope>test</scope>
                      </dependency>
                      <!-- shiro -->
                      <dependency>
                          <groupId>org.apache.shiro</groupId>
                          <artifactId>shiro-spring</artifactId>
                          <version>${spring.shiro.version}</version>
                      </dependency>

                      <dependency>
                          <groupId>org.projectlombok</groupId>
                          <artifactId>lombok</artifactId>
                          <optional>true</optional>
                      </dependency>
                      <!--页面模板依赖-->
                      <dependency>
                          <groupId>org.springframework.boot</groupId>
                          <artifactId>spring-boot-starter-thymeleaf</artifactId>
                      </dependency>
                      <!--热部署依赖-->
                      <dependency>
                          <groupId>org.springframework.boot</groupId>
                          <artifactId>spring-boot-devtools</artifactId>
                          <scope>runtime</scope>
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
