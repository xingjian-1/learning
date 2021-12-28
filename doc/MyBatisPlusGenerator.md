##### 	Mybatis Plus 的代码生成器 (Oracle) 
模板引擎是 velocity(默认引擎),使用自定义模板生成支持Swagger2的实体类，生成的@ApiModelProperty中的value值都是数据库表字段的注释，所有生成之前数据表一定要先注释好
##### 引入maven依赖

    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus-generator</artifactId>
        <version>3.1.1</version>
    </dependency>
##### 代码生成工具类

##### 在resources目录下创建templates目录
##### 在templates目录下创建entity.java.vm模板(代码如下)

