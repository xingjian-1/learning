#### 第一种：使用spring自带的DriverManagerDataSource
配置文件如下：

        <?xml version="1.0" encoding="UTF-8"?>   
        <beans xmlns="http://www.springframework.org/schema/beans"   
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"   
        xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"   
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-2.5.xsd   
        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-2.5.xsd   
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-2.5.xsd   
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-2.5.xsd">   

        <bean id="dataSource"  
        class="org.springframework.jdbc.datasource.DriverManagerDataSource">  
        <property name="driverClassName">   
        <value>oracle.jdbc.driver.OracleDriver</value>   
        </property>   
        <property name="url">   
        <value>jdbc:oracle:thin:@192.168.24.102:1521:sms</value>   
        </property>   
        <property name="username">   
        <value>test</value>   
        </property>   
        <property name="password">   
        <value>test</value>   
        </property>   
        </bean>   
        </beans>   
 Spring本身提供了一个简单的数据源实现类DriverManagerDataSource ，它位于org.springframework.jdbc.datasource包中。这个类实现了javax.sql.DataSource接口，但 它并没有提供池化连接的机制，每次调用getConnection()获取新连接时，只是简单地创建一个新的连接。因此，这个数据源类比较适合在单元测试 或简单的独立应用中使用，因为它不需要额外的依赖类。 
     下面，我们来看一下DriverManagerDataSource的简单使用：当然，我们也可以通过上面配置的方式直接使用DriverManagerDataSource。

      DriverManagerDataSource ds = new DriverManagerDataSource ();      
      ds.setDriverClassName("com.mysql.jdbc.Driver");      
      ds.setUrl("jdbc:oracle:thin:@192.168.24.102:1521:sms");      
      ds.setUsername("test");      
      ds.setPassword("test");      
      Connection actualCon = ds.getConnection();  
#### 第二种：DBCP数据源

Spring在第三方依赖包中包含了两个数据源的实现类包，其一是Apache的DBCP，其二是 C3P0。可以在Spring配置文件中利用这两者中任何一个配置数据源。

DBCP类包位于<spring_home/>/lib/jakarta-commons/commons-dbcp.jar，DBCP是一个依赖 Jakarta commons-pool对象池机制的数据库连接池，所以在类路径下还必须包括<spring_home/>/lib/jakarta- commons/commons-pool.jar。另外还需要个commons-collections.jar包项，它是目组中的一个各种集合类和集合工具类的封装，类似C++中的Boost库，是对Java容器类型和算法的补充，为Java标准的Collections API提供了相当好的补充和拓展。

下面是使用DBCP配置MySql数据源的配置片断：

      <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"       
              destroy-method="close">       
          <property name="driverClassName" value="com.mysql.jdbc.Driver" />      
          <property name="url" value="jdbc:mysql://localhost:3309/sampledb" />      
          <property name="username" value="root" />      
          <property name="password" value="1234" />      
      </bean>  

BasicDataSource提供了close()方法关闭数据源，所以必须设定destroy-method=”close”属性， 以便Spring容器关闭时，数据源能够正常关闭。除以上必须的数据源属性外，还有一些常用的属性： 
    defaultAutoCommit：设置从数据源中返回的连接是否采用自动提交机制，默认值为 true； 
    defaultReadOnly：设置数据源是否仅能执行只读操作， 默认值为 false； 
    maxActive：最大连接数据库连接数，设置为0时，表示没有限制； 
    maxIdle：最大等待连接中的数量，设置为0时，表示没有限制； 
    maxWait：最大等待秒数，单位为毫秒， 超过时间会报出错误信息； 
    validationQuery：用于验证连接是否成功的查询SQL语句，SQL语句必须至少要返回一行数据， 如你可以简单地设置为：“select count(*) from user”； 
    removeAbandoned：是否自我中断，默认是 false ； 
    removeAbandonedTimeout：几秒后数据连接会自动断开，在removeAbandoned为true，提供该值； 
    logAbandoned：是否记录中断事件， 默认为 false；

#### 第三种：C3P0数据源

C3P0是一个开放源代码的JDBC数据源实现项目，它在lib目录中与Hibernate一起发布，实现了JDBC3和JDBC2扩展规范说明的 Connection 和Statement 池。C3P0类包位于<spring_home/>/lib/c3p0/c3p0-0.9.0.4.jar。下面是使用C3P0配置一个 Oracle数据源：

      <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource"       
              destroy-method="close">      
          <property name="driverClass" value=" oracle.jdbc.driver.OracleDriver "/>      
          <property name="jdbcUrl" value=" jdbc:oracle:thin:@localhost:1521:ora9i "/>      
          <property name="user" value="admin"/>      
          <property name="password" value="1234"/>      
      </bean>  

ComboPooledDataSource和BasicDataSource一样提供了一个用于关闭数据源的close()方法，这样我们就可以保证Spring容器关闭时数据源能够成功释放。 
    C3P0拥有比DBCP更丰富的配置属性，通过这些属性，可以对数据源进行各种有效的控制： 
    acquireIncrement：当连接池中的连接用完时，C3P0一次性创建新连接的数目； 
    acquireRetryAttempts：定义在从数据库获取新连接失败后重复尝试获取的次数，默认为30； 
    acquireRetryDelay：两次连接中间隔时间，单位毫秒，默认为1000； 
    autoCommitOnClose：连接关闭时默认将所有未提交的操作回滚。默认为false； 
    automaticTestTable： C3P0将建一张名为Test的空表，并使用其自带的查询语句进行测试。如果定义了这个参数，那么属性preferredTestQuery将被忽略。你 不能在这张Test表上进行任何操作，它将中为C3P0测试所用，默认为null； 
    breakAfterAcquireFailure：获取连接失败将会引起所有等待获取连接的线程抛出异常。但是数据源仍有效保留，并在下次调   用getConnection()的时候继续尝试获取连接。如果设为true，那么在尝试获取连接失败后该数据源将申明已断开并永久关闭。默认为 false； 
    checkoutTimeout：当连接池用完时客户端调用getConnection()后等待获取新连接的时间，超时后将抛出SQLException，如设为0则无限期等待。单位毫秒，默认为0； 
    connectionTesterClassName： 通过实现ConnectionTester或QueryConnectionTester的类来测试连接，类名需设置为全限定名。默认为 com.mchange.v2.C3P0.impl.DefaultConnectionTester； 
    idleConnectionTestPeriod：隔多少秒检查所有连接池中的空闲连接，默认为0表示不检查； 
    initialPoolSize：初始化时创建的连接数，应在minPoolSize与maxPoolSize之间取值。默认为3； 
    maxIdleTime：最大空闲时间，超过空闲时间的连接将被丢弃。为0或负数则永不丢弃。默认为0； 
    maxPoolSize：连接池中保留的最大连接数。默认为15； 
    maxStatements：JDBC的标准参数，用以控制数据源内加载的PreparedStatement数量。但由于预缓存的Statement属 于单个Connection而不是整个连接池。所以设置这个参数需要考虑到多方面的因素，如果maxStatements与 maxStatementsPerConnection均为0，则缓存被关闭。默认为0； 
    maxStatementsPerConnection：连接池内单个连接所拥有的最大缓存Statement数。默认为0； 
    numHelperThreads：C3P0是异步操作的，缓慢的JDBC操作通过帮助进程完成。扩展这些操作可以有效的提升性能，通过多线程实现多个操作同时被执行。默认为3； 
    preferredTestQuery：定义所有连接测试都执行的测试语句。在使用连接测试的情况下这个参数能显著提高测试速度。测试的表必须在初始数据源的时候就存在。默认为null； 
    propertyCycle： 用户修改系统配置参数执行前最多等待的秒数。默认为300； 
    testConnectionOnCheckout：因性能消耗大请只在需要的时候使用它。如果设为true那么在每个connection提交的时候都 将校验其有效性。建议使用idleConnectionTestPeriod或automaticTestTable 
等方法来提升连接测试的性能。默认为false； 
    testConnectionOnCheckin：如果设为true那么在取得连接的同时将校验连接的有效性。默认为false。

读配置文件的方式引用属性：

        <bean id="propertyConfigurer"  class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">      
            <property name="location" value="/WEB-INF/jdbc.properties"/>      
        </bean>      
        <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"       
                destroy-method="close">      
            <property name="driverClassName" value="${jdbc.driverClassName}" />      
            <property name="url" value="${jdbc.url}" />      
            <property name="username" value="${jdbc.username}" />      
            <property name="password" value="${jdbc.password}" />      
        </bean>   

在jdbc.properties属性文件中定义属性值：
    jdbc.driverClassName= com.mysql.jdbc.Driver 
    jdbc.url= jdbc:mysql://localhost:3309/sampledb 
    jdbc.username=root 
    jdbc.password=1234 
    提示 经常有开发者在${xxx}的前后不小心键入一些空格，这些空格字符将和变量合并后作为属性的值。如：<property name="username" value=" ${jdbc.username} "></property>的属性配置项，在前后都有空格，被解析后，username的值为“ 1234 ”，这将造成最终的错误，因此需要特别小心。

#### 第四种：JNDI数据源

如果应用配置在高性能的应用服务器（如WebLogic或Websphere等）上，我们可能更希望使用应用服务器本身提供的数据源。应用服务器的数据源 使用JNDI开放调用者使用，Spring为此专门提供引用JNDI资源的JndiObjectFactoryBean类。下面是一个简单的配置例子：


          <!-- WTPを利用してTomcatを起動する場合、<ContextRoot>/META-INF/context.xmlに データソースの接続情報を設定する。 
              またTomcatでJDBC接続を行う場合、各種JDBCドライバのjarにクラスパスを通すこと。 -->
          <bean id="TerasolunaSampleDataSource" class="org.springframework.jndi.JndiObjectFactoryBean">
              <property name="jndiName">
                  <!-- Tomcatの例 -->
                  <value>java:comp/env/jdbc/TerasolunaSampleDataSource</value>
                  <!-- Tomcat以外のAPサーバの例 -->
                  <!-- <value>jdbc/TerasolunaSampleDataSource</value> -->
              </property>
          </bean>
通过jndiName指定引用的JNDI数据源名称。 
    Spring 2.0为获取J2EE资源提供了一个jee命名空间，通过jee命名空间，可以有效地简化J2EE资源的引用。下面是使用jee命名空间引用JNDI数据源的配置：

        <beans xmlns=http://www.springframework.org/schema/beans    
        xmlns:xsi=http://www.w3.org/2001/XMLSchema-instance    
        xmlns:jee=http://www.springframework.org/schema/jee    
        xsi:schemaLocation="http://www.springframework.org/schema/beans     
        http://www.springframework.org/schema/beans/spring-beans-2.0.xsd     
        http://www.springframework.org/schema/jee    
        http://www.springframework.org/schema/jee/spring-jee-2.0.xsd">      
        <jee:jndi-lookup id="dataSource" jndi-name="java:comp/env/jdbc/TerasolunaSampleDataSource"/>      
        </beans>  

      附在<ContextRoot>/META-INF/context.xml的DataSource链接情报：

              <Context>
                <Resource
                   name="jdbc/TerasolunaSampleDataSource"
                   type="javax.sql.DataSource"
                   driverClassName="org.postgresql.Driver"
                   username="togodb"
                   password="togo"
                   url="jdbc:postgresql://127.0.0.1:5432/method_db"
                   maxIdle="2"
                   maxWait="5000"
                   maxActive="4"/>
              </Context>
