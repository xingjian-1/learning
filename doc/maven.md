##### maven
Maven 项目对象模型(POM)，Maven这个单词来自于意第绪语（犹太语），意为知识的积累，最初在Jakata Turbine项目中用来简化构建过程。
##### maven生命周期

                    maven生命周期：
                    验证 validate	验证项目	验证项目是否正确且所有必须信息是可用的
                    编译 compile	执行编译	源代码编译在此阶段完成
                    测试 Test	测试	使用适当的单元测试框架（例如JUnit）运行测试。
                    包装 package	打包	创建JAR/WAR包如在 pom.xml 中定义提及的包
                    检查 verify	检查	对集成测试的结果进行检查，以保证质量达标
                    安装 install	安装	安装打包的项目到本地仓库，以供其他项目使用
                    部署 deploy	部署	拷贝最终的工程包到远程仓库中，以共享给其他开发人员和工程
#### Maven仓库相关
     阿里中央仓库：
            <repository>  
                <id>alimaven</id>
                <name>aliyun maven</name>
                <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
            </repository> 

            camunda.com 中央仓库：
            <repository>  
                <id>activiti-repos2</id>  
                <name>Activiti Repository 2</name>  
                <url>https://app.camunda.com/nexus/content/groups/public</url>  
            </repository>

            spring.io 中央仓库：
            <repository>  
                <id>springsource-repos</id>  
                <name>SpringSource Repository</name>  
                <url>http://repo.spring.io/release/</url>  
            </repository>

            maven.apache.org 中央仓库：
            <repository>  
                <id>central-repos</id>  
                <name>Central Repository</name>  
                <url>http://repo.maven.apache.org/maven2</url>  
            </repository>

            maven.org 中央仓库：
            <repository>  
                <id>central-repos1</id>  
                <name>Central Repository 2</name>  
                <url>http://repo1.maven.org/maven2/</url>  
            </repository>

            alfresco.com 中央仓库：
            <repository>  
                <id>activiti-repos</id>  
                <name>Activiti Repository</name>  
                <url>https://maven.alfresco.com/nexus/content/groups/public</url>  
            </repository>
##### 
maven的核心就是pom.xml，使用maven是为了更好的在项目里面管理包依赖。如果要引入一个jar包，需要在pom文件中加上<dependency></dependency>就可以依赖相应的jar包。

场景一，有三个项目J1、J2，J3依赖同一个jar包(domain.jar),如果分别在各自pom文件中引入domain.jar的依赖,当domain.jar的版本发生改变时，三个项目的pom文件都要改，项目中依赖这类的jar包越多每次版本更新时都需要在每个项目里面一个一个修改。为了解决这个问题，就需要创建一个parent项目, 打包类型为pom，没有任何代码，只是管理多个项目之间公共的依赖。在parent项目的pom文件中定义对domain.jar的依赖，三个子项目中只需要依赖parent就可以了。例如：
parent项目:

    <groupId>cn.getech.poros</groupId>
    <artifactId>poros-parent</artifactId>
    <packaging>pom</packaging>
    <version>poros-1.0.0</version>
其他的项目中就可以这样引用：

<parent>
    <artifactId>poros-parent</artifactId>
    <groupId>cn.getech.poros</groupId>
    <version>poros-1.0.0</version>
  </parent>

场景二，如果J1、J2项目需要这个jar包，J3项目不需要怎么办。<dependencyManagement></dependencyManagement>将公共依赖的jar包管理起来，哪个子项目要用，就在自己的pom文件中再加引用，例如：
parent：
   <groupId>cn.getech.poros</groupId>
    <artifactId>poros-parent</artifactId>
    <packaging>pom</packaging>
    <version>poros-1.0.0</version>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <org.mapstruct.version>1.3.1.Final</org.mapstruct.version>
        <java.version>1.8</java.version>
        <spring-boot.version>2.3.3.RELEASE</spring-boot.version>
        <spring-cloud.version>Hoxton.SR7</spring-cloud.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <!-- Spring Cloud -->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>

            <!-- Spring Cloud Alibaba -->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>${spring-cloud-alibaba.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--Nacos-->
            <dependency>
                <groupId>com.alibaba.nacos</groupId>
                <artifactId>nacos-client</artifactId>
                <version>${nacos.client.version}</version>
            </dependency>
            <dependency>
                <groupId>com.alibaba.nacos</groupId>
                <artifactId>nacos-config</artifactId>
                <version>${nacos.config.version}</version>
            </dependency>
            <!-- Swagger -->
            <dependency>
                <groupId>io.springfox</groupId>
                <artifactId>springfox-swagger2</artifactId>
                <version>${springfox-swagger2.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
需要依赖的子项目：
<parent>
    <artifactId>poros-parent</artifactId>
    <groupId>cn.getech.poros</groupId>
    <version>poros-1.0.0</version>
  </parent>
<!--当前项目私有的jar包 -->
<dependencyManagement>
<dependencies>
          <!--Mybatis Plus -->
            <dependency>
                <groupId>com.baomidou</groupId>
                <artifactId>mybatis-plus-boot-starter</artifactId>
                <version>${mybatis-plus.version}</version>
            </dependency>
</dependencies>
</dependencyManagement>

<!--引用父类的公告jar包 -->
<dependencies>
<dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
 </dependency>
</dependencies>

##### 属性解析
<modelVersion>4.0.0</modelVersion>：指定了当前POM模型的版本，对于Maven2及Maven 3来说，它只能是4.0.0。
<groupId>和<artifactId>：groupid和artifactId被统称为“坐标”是为了保证项目唯一性而提出的，如果要把一个项目放到maven本地仓库去，然后再从仓库里面去查找项目就时根据这两个id去查找的。
<packaging>：属性表示项目的打包类型【默认：jar类型】。
<properties>：在POM中的<properties />元素中，<properties><someVar>value</someVar></properties>的值可以用作${someVar}。${project.basedir}：这引用了module/project的根文件夹（当前pom.xml文件所在的位置），还可以简化的写法：${basedir}
${project.build.directory}：这表示默认的target文件夹。

${project.build.outputDirectory}：默认情况下表示target/classes文件夹。

${project.build.testOutputDirectory}：这表示默认的target/test-classes文件夹。

${project.build.sourceDirectory}：这表示默认情况下src/main/java文件夹。

${project.build.testSourceDirectory}：这表示默认情况下src/test/java文件夹。

${project.build.finalName}：默认情况下定义为${project.artifactId}-${project.version}。

${project.version}：这可以在必须编写文字版本的位置使用，否则，特别是如果您在多模块构建模块间依赖关系
例子1：
   <properties>
        <test.uuid>123</test.uuid>
        <jdbc.driverClassName>com.mysql.jdbc.Driver</jdbc.driverClassName>
        <jdbc.url>jdbc:mysql://localhost/stock?createDatabaseIfNotExist=true&amp;useUnicode=true&amp;characterEncoding=utf-8&amp;autoReconnect=true</jdbc.url>
        <jdbc.username>root</jdbc.username>
        <jdbc.password>123456</jdbc.password>
        <jdbc.validationQuery>SELECT 1 + 1</jdbc.validationQuery>
    </properties>
例子2：
<properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <org.mapstruct.version>1.3.1.Final</org.mapstruct.version>
        <java.version>1.8</java.version>
        <spring-boot.version>2.3.3.RELEASE</spring-boot.version>
        <spring-framework.version>5.2.8.RELEASE</spring-framework.version>
        <spring-boot-admin.version>2.3.0</spring-boot-admin.version>
        <spring-cloud.version>Hoxton.SR7</spring-cloud.version>
        <spring-cloud-alibaba.version>2.2.1.RELEASE</spring-cloud-alibaba.version>
        <lombok.version>1.18.12</lombok.version>
        <fastjson.version>1.2.73</fastjson.version>
        <commons-lang3.version>3.11</commons-lang3.version>
        <commons-beanutils.version>1.9.4</commons-beanutils.version>
        <mysql.driver.version>8.0.13</mysql.driver.version>
        <hutool.version>5.3.10</hutool.version>
        <mybatis-plus.version>3.3.2</mybatis-plus.version>
        <druid-starter.version>1.1.22</druid-starter.version>
        <velocity.version>2.1</velocity.version>
        <easypoi.version>4.2.0</easypoi.version>
        <poi.version>4.1.2</poi.version>
        <knife4j.version>2.0.4</knife4j.version>
        <springfox-swagger2.version>2.9.2</springfox-swagger2.version>
        <skywalking.version>7.0.0</skywalking.version>
        <jwt.version>0.9.1</jwt.version>
        <swagger-models.version>1.5.22</swagger-models.version>
        <xxl-job.version>2.1.2</xxl-job.version>
        <commons-io.version>2.6</commons-io.version>
        <guava.version>29.0-jre</guava.version>
        <jackson-databind.version>2.11.2</jackson-databind.version>
        <common-pool2.version>2.8.1</common-pool2.version>
        <commons-text.version>1.9</commons-text.version>
        <sentinel.version>1.7.2</sentinel.version>
        <oss.version>3.1.0</oss.version>
        <seata.version>1.3.0</seata.version>
        <nacos.client.version>1.3.1</nacos.client.version>
        <nacos.config.version>0.8.0</nacos.config.version>
        <seata.version>1.4.1</seata.version>
    </properties>
<scope>:在maven项目中，如果存在编译需要而发布不需要的jar包，可以用scope标签，值设为provided。如下：
<dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <scope>provided</scope>
        </dependency>
<scope>其他参数如下：
compile：默认值，表示dependency的都可以在生命周期中使用。而且，这些dependencies 会传递到依赖的项目中。适用于所有阶段，会随着项目一起发布
provided：跟compile相似，但是表明了dependency 由JDK或者容器提供，例如Servlet AP和一些Java EE APIs。这个scope 只能作用在编译和测试时，同时没有传递性。
runtime：表示dependency不作用在编译时，但会作用在运行和测试时，如JDBC驱动，适用运行和测试阶段。
test：表示dependency作用在测试时，不作用在运行时。 只在测试时使用，用于编译和运行测试代码。不会随项目发布。
system：跟provided 相似，但是在系统中要以外部JAR包的形式提供，maven不会在repository查找它。

<exclusions>：A项目依赖B项目，如果A项目不需要B项目里面某一个jar包，可以使用这个属性去掉不需要的那个jar包。例子：
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>com.vaadin.external.google</groupId>
                    <artifactId>android-json</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
<build>：Maven的pom.xml文件中，存在两种<build>
1.全局配置（project build），是<project>的直接子元素针，对整个项目的所有情况都有效
2.profile build是<profile>的直接子元素， 针对不同的profile配置

<project xmlns="http://maven.apache.org/POM/4.0.0"  
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">  
  ...  
  <!-- "Project Build" contains elements of the BaseBuild set and the Build set-->  
  <build>...</build>  
   
  <profiles>  
    <profile>  
      <!-- "Profile Build" contains elements of the BaseBuild set only -->  
      <build>...</build>  
    </profile>  
  </profiles>  
</project>
全局build：

<build>  
  <defaultGoal>install</defaultGoal>  
  <directory>${basedir}/target</directory>  
  <finalName>${artifactId}-${version}</finalName>   <filters>   <filter>filters/filter1.properties</filter>  </filters>   ...</build>
属性解析：
1）defaultGoal
                    执行build任务时，如果没有指定目标，将使用的默认值。
                    如上配置：在命令行中执行mvn，则相当于执行mvn install
              2）directory
                     build目标文件的存放目录，默认在${basedir}/target目录
              3）finalName
                     build目标文件的名称，默认情况为${artifactId}-${version}
              4）filter

                     定义*.properties文件，包含一个properties列表，该列表会应用到支持filter的resources中。
                     也就是说，定义在filter的文件中的name=value键值对，会在build时代替${name}值应用到resources中。
                     maven的默认filter文件夹为${basedir}/src/main/filters
Resources配置：用于包含或者排除某些资源文件

<build>  
        ...  
       <resources>  
          <resource>  
             <targetPath>META-INF/plexus</targetPath>  
             <filtering>true</filtering>  
            <directory>${basedir}/src/main/plexus</directory>  
            <includes>  
                <include>configuration.xml</include>  
            </includes>  
            <excludes>  
                <exclude>**/*.properties</exclude>  
            </excludes>  
         </resource>  
    </resources>  
    <testResources>  
        ...  
    </testResources>  
    ...  
</build>
解析：
1）resources
                    一个resources元素的列表。每一个都描述与项目关联的文件是什么和在哪里
              2）targetPath
                    指定build后的resource存放的文件夹，默认是basedir。
                    通常被打包在jar中的resources的目标路径是META-INF
             3）filtering
                    true/false，表示为这个resource，filter是否激活
             4）directory
                    定义resource文件所在的文件夹，默认为${basedir}/src/main/resources
             5）includes
                    指定哪些文件将被匹配，以*作为通配符
             6）excludes
                   指定哪些文件将被忽略
             7）testResources
                   定义和resource类似，只不过在test时使用

plugins配置：用于指定使用的插件

<build>  
    ...  
    <plugins>  
        <plugin>  
            <groupId>org.apache.maven.plugins</groupId>  
            <artifactId>maven-jar-plugin</artifactId>  
            <version>2.0</version>  
            <extensions>false</extensions>  
            <inherited>true</inherited>  
            <configuration>  
                <classifier>test</classifier>  
            </configuration>  
            <dependencies>...</dependencies>  
            <executions>...</executions>  
        </plugin>  
    </plugins>  
</build>

pluginManagement配置： pluginManagement的配置和plugins的配置是一样的，只是用于继承，使得可以在孩子pom中使用。
父pom：
<build>  
    ...  
    <pluginManagement>  
        <plugins>  
            <plugin>  
              <groupId>org.apache.maven.plugins</groupId>  
              <artifactId>maven-jar-plugin</artifactId>  
              <version>2.2</version>  
                <executions>  
                    <execution>  
                        <id>pre-process-classes</id>  
                        <phase>compile</phase>  
                        <goals>  
                            <goal>jar</goal>  
                        </goals>  
                        <configuration>  
                            <classifier>pre-process</classifier>  
                        </configuration>  
                    </execution>  
                </executions>  
            </plugin>  
        </plugins>  
    </pluginManagement>  
    ...  
</build>
在子pom中，只需要配置：
<build>  
    ...  
    <plugins>  
        <plugin>  
            <groupId>org.apache.maven.plugins</groupId>  
            <artifactId>maven-jar-plugin</artifactId>  
        </plugin>  
    </plugins>  
    ...  
</build>
这样大大简化了子pom的配置

<modules>：
项目A下有一个pom.xml。
项目A下有两个module，每个module中有自己的pom.xml。
在项目A的pom中加入<modules>标签，将两个module加入其中。
在构建这个项目的时候，不需要深入每个module去单独构建，而只是在项目A下的pom.xml构建，就会完成对两个module的构建。
如果项目下多module，modules标签的优势将更加明显。
