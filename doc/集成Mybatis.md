#### SpringCloud集成Mybaits步骤，MyBatis：一款优秀的轻量级半自动持久层框架，与之相对应的还有hibernate框架。
*  添加Mysql依赖

        <!-- 引用Mybatis 和 Mysql驱动开始  -->
                <dependency>
                    <groupId>org.mybatis.spring.boot</groupId>
                    <artifactId>mybatis-spring-boot-starter</artifactId>
                    <version>1.3.0</version>
                </dependency>
                <dependency>
                    <groupId>mysql</groupId>
                    <artifactId>mysql-connector-java</artifactId>
                </dependency>
        <!-- 引用Mybatis 和 Mysql驱动结束  -->
*  配置反向生成工具对数据库表生成相应的实体类和配置文件，在pom.xml文件添加插件引用

       <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.5</version>
                <dependencies>
                    <dependency>
                        <groupId> mysql</groupId>
                        <artifactId> mysql-connector-java</artifactId>
                        <version> 5.1.39</version>
                    </dependency>
                    <dependency>
                        <groupId>org.mybatis.generator</groupId>
                        <artifactId>mybatis-generator-core</artifactId>
                        <version>1.3.5</version>
                    </dependency>
                </dependencies>
                <executions>
                    <execution>
                        <id>Generate MyBatis Artifacts</id>
                        <phase>package</phase>
                        <goals>
                            <goal>generate</goal>
                        </goals>
                    </execution>
                </executions>
                <configuration>
                    <!--允许移动生成的文件 -->
                    <verbose>true</verbose>
                    <!-- 是否覆盖 -->
                    <overwrite>true</overwrite>
                    <!-- 自动生成的配置 -->
                    <configurationFile>
                        src/main/resources/mybatis-generator.xml
                    </configurationFile>
                </configuration>
            </plugin>
   * 新建配置文件：generatorConfig.xml
   
           <?xml version="1.0" encoding="UTF-8"?>
            <!DOCTYPE generatorConfiguration
                    PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
                    "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
            <generatorConfiguration>
            <!--mysql 连接数据库jar 这里选择自己本地位置-->
            <classPathEntry location="F:\dev-space\repo\mysql\mysql-connector-java\5.1.45\mysql-connector-java-5.1.45.jar" />
            <context id="testTables" targetRuntime="MyBatis3">
                <commentGenerator>
                    <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
                    <property name="suppressAllComments" value="true" />
                </commentGenerator>
                <!--数据库连接的信息：驱动类、连接地址、用户名、密码 -->
                <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                                connectionURL="jdbc:mysql://192.168.1.100:3306/test?serverTimezone=UTC" userId="bdgascloud"
                                password="bdgascloud">
                </jdbcConnection>
                <!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL 和
                   NUMERIC 类型解析为java.math.BigDecimal -->
                <javaTypeResolver>
                    <property name="forceBigDecimals" value="false" />
                </javaTypeResolver>

                <!-- targetProject:生成PO类的位置 -->
                <javaModelGenerator targetPackage="com.didispace.web.jdbc.po"
                                    targetProject="src/main/java">
                    <!-- enableSubPackages:是否让schema作为包的后缀 -->
                    <property name="enableSubPackages" value="false" />
                    <!-- 从数据库返回的值被清理前后的空格 -->
                    <property name="trimStrings" value="true" />
                </javaModelGenerator>
                <!-- targetProject:mapper映射文件生成的位置
                   如果maven工程只是单独的一个工程，targetProject="src/main/java"
                   若果maven工程是分模块的工程，targetProject="所属模块的名称"，例如：
                   targetProject="ecps-manager-mapper"，下同-->
                <sqlMapGenerator targetPackage="mapXml"
                                 targetProject="src/main/resources">
                    <!-- enableSubPackages:是否让schema作为包的后缀 -->
                    <property name="enableSubPackages" value="false" />
                </sqlMapGenerator>
                <!-- targetPackage：mapper接口生成的位置 -->
                <javaClientGenerator type="XMLMAPPER"
                                     targetPackage="com.didispace.web.jdbc.mapper"
                                     targetProject="src/main/java">
                    <!-- enableSubPackages:是否让schema作为包的后缀 -->
                    <property name="enableSubPackages" value="false" />
                </javaClientGenerator>
                <!-- 指定数据库表 -->
                <table schema="" tableName="user"></table>③
            </context>
          </generatorConfiguration>
  * 先maven–>update project，在Maven build菜单里面配置运行参数：Goals：mybatis-generator:generate -e Profiles：generatorConfig.xml 然后点击run运行成功之后，会生成mapper、实体类文件
  * applicable.properties文件中添加
               
                mybatis.typeAliasesPackage=com.didispace.web.jdbc.po  ⑤
                mybatis.mapperLocations=classpath:mapXml/UserMapper.xml
  
  *  启动类添加：@MapperScan("com.didispace.web.jdbc.mapper")
  * 执行Controller类的方法：
  
             @RestController
             public class HelloController {
                 @Autowired
                  private UserMapper userMapper;
                  
                  @RequestMapping("/hello")
                  public String index() {
                            User  user = userMapper.selectByPrimaryKey(1);
                         return "Hello SpringBoot"+user.getName();
                     
                 }     
             }
  
