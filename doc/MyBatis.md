###### 生如蝼蚁 当立鸿鹄之志 命薄如纸 应有不屈之心 大丈夫生于天地间 岂能郁郁久居人下 乾坤未定 你我皆是黑马
---------------
#### 框架概述
MyBatis本是apache的一个开源项目iBatis, 2010年这个项目由apache software foundation 迁移到了google code，并且改名为MyBatis 。2013年11月迁移到Github。iBATIS一词来源于“internet”和“abatis”的组合，是一个基于Java的持久层框架。使用XML或注解来配置和映射原生信息，将接口和Java的POJO(Plain Ordinary Java Object,普通的Java对象)映射成数据库中的记录 --来源自百度百科 https://mybatis.org/mybatis-3/getting-started.html<br>
MyBatis-Plus (opens new window)（简称 MP）是一个 MyBatis (opens new window)的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。https://mp.baomidou.com/guide/

`特点`：轻量级、解耦(业务逻辑和数据访问分离，使系统结构更清晰，易维护，更易单元测试)<br>
`结构`:
* API接口层：提供一些API接口，通过这些API接口来操纵数据库,接口层收到调用请求会调用数据处理层来完成具体的数据处理。
* 数据处理层：负责具体的SQL查找、SQL解析、SQL执行和执行结果映射处理等,主要目的是根据调用的请求完成一次数据库操作。
* 基础支撑层：负责最基础的功能支撑，包括连接管理、事务管理、配置加载和缓存处理，这些都是共用的功能，将他们抽取出来作为最基础的组件，为上层的数据处理层提供最基础的支撑。 

                       <!--通用Mapper-->
                       <dependency>
                           <groupId>tk.mybatis</groupId>
                           <artifactId>mapper</artifactId>
                           <version>4.0.4</version>
                       </dependency>
#### MyBatis初始化流程代码示例
 
                        String resource = "mybatis-config.xml";
                        InputStream inputStream = Resources.getResourceAsStream(resource);
                        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
                        SqlSession sqlSession = sqlSessionFactory.openSession();
                        List list = sqlSession.selectList("com.foo.bean.BlogMapper.queryAllBlogInfo");
* 创建Configuration对象
MyBatis初始化时会执行SqlSessionFactoryBuilder中的build()方法,build方法调用XMLConfigBuilder()的parse()方法返回Configuration对象。

                public Configuration parse(){
                    if (parsed){
                        throw new BuilderException("Each XMLConfigBuilder can only be used once.");
                    }
                    parsed = true;
                    //源码中没有这一句，只有 parseConfiguration(parser.evalNode("/configuration"));
                    //为了让读者看得更明晰，源码拆分为以下两句
                    XNode configurationNode = parser.evalNode("/configuration");
                    parseConfiguration(configurationNode);
                    return configuration;
                }
              //configuration"节点下的子节点信息，然后将解析的结果设置到Configuration对象中
              private void parseConfiguration(XNode root) {
                try {
                  //1.首先处理properties 节点	
                  propertiesElement(root.evalNode("properties")); //issue #117 read properties first
                  //2.处理typeAliases
                  typeAliasesElement(root.evalNode("typeAliases"));
                  //3.处理插件
                  pluginElement(root.evalNode("plugins"));
                  //4.处理objectFactory
                  objectFactoryElement(root.evalNode("objectFactory"));
                  //5.objectWrapperFactory
                  objectWrapperFactoryElement(root.evalNode("objectWrapperFactory"));
                  //6.settings
                  settingsElement(root.evalNode("settings"));
                  //7.处理environments
                  environmentsElement(root.evalNode("environments")); // read it after objectFactory and objectWrapperFactory issue #631
                  //8.database
                  databaseIdProviderElement(root.evalNode("databaseIdProvider"));
                  //9. typeHandlers
                  typeHandlerElement(root.evalNode("typeHandlers"));
                  //10 mappers
                  mapperElement(root.evalNode("mappers"));
                } catch (Exception e) {
                  throw new BuilderException("Error parsing SQL Mapper Configuration. Cause: " + e, e);
                }
              }
* 创建sqlSessionFactory
上面的一切操作都是为了生成一个sqlSessionFactory

             public SqlSessionFactory build(InputStream inputStream, String environment, Properties properties){
                            try{
                                //2. 创建XMLConfigBuilder对象用来解析XML配置文件，生成Configuration对象
                                XMLConfigBuilder parser = new XMLConfigBuilder(inputStream, environment, properties);
                                //3. 将XML配置文件内的信息解析成Java对象Configuration对象
                                Configuration config = parser.parse();
                                //4. 根据Configuration对象创建出SqlSessionFactory对象
                                return build(config);
                            }catch (Exception e){
                                throw ExceptionFactory.wrapException("Error building SqlSession.", e);
                            }finally{
                                ErrorContext.instance().reset();
                                try{
                                    inputStream.close();
                                }
                                catch (IOException e){}
                            }
                        }
                        //从此处可以看出，MyBatis通过Configuration对象来创建SqlSessionFactory,用户也可以自己通过API构造好Configuration对象，调用此方法创建SqlSessionFactory
                        public SqlSessionFactory build(Configuration config){
                            return new DefaultSqlSessionFactory(config);
                        }
                        
                        涉及到了以下几个对象：

                        SqlSessionFactoryBuilder ： SqlSessionFactory的构造器，用于创建SqlSessionFactory，采用了Builder设计模式
                        Configuration ：mybatis-config.xml文件中mybatis配置信息
                        SqlSessionFactory：SqlSession工厂类，以工厂形式创建SqlSession对象，采用了Factory工厂设计模式
                        XmlConfigParser ：负责将mybatis-config.xml配置文件解析成Configuration对象
* 创建sqlSession

            public SqlSession openSession() {
                return openSessionFromDataSource(configuration.getDefaultExecutorType(), null, false);
              }
            private SqlSession openSessionFromDataSource(ExecutorType execType, 
            TransactionIsolationLevel level, boolean autoCommit) {
                Transaction tx = null;
                try {
                  final Environment environment = configuration.getEnvironment();
                  final TransactionFactory transactionFactory = getTransactionFactoryFromEnvironment(environment);
                  tx = transactionFactory.newTransaction(environment.getDataSource(), level, autoCommit);
                  final Executor executor = configuration.newExecutor(tx, execType);
                  return new DefaultSqlSession(configuration, executor, autoCommit);
                } catch (Exception e) {
                  closeTransaction(tx); // may have fetched a connection so lets call close()
                  throw ExceptionFactory.wrapException("Error opening session.  Cause: " + e, e);
                } finally {
                  ErrorContext.instance().reset();
                }
              }
            //返回一个SqlSession，默认使用DefaultSqlSession
             public DefaultSqlSession(Configuration configuration, Executor executor, boolean autoCommit) {
                this.configuration = configuration;
                this.executor = executor;
                this.dirty = false;
                this.autoCommit = autoCommit;
              }
* 执行具体的SQL

            要执行User user = sqlSession.selectOne("test.findUserById", 1);
            public <E> List<E> selectList(String statement, Object parameter, RowBounds rowBounds) {
                try {
                 //1.根据Statement Id，在mybatis 配置对象Configuration中查找和配置文件相对应的MappedStatement
                  MappedStatement ms = configuration.getMappedStatement(statement);
                  //2. 将查询任务委托给MyBatis的执行器Executor
                  List<E> result = executor.query(ms, wrapCollection(parameter), rowBounds, Executor.NO_RESULT_HANDLER);
                  return result;
                } catch (Exception e) {
                  throw ExceptionFactory.wrapException("Error querying database.  Cause: " + e, e);
                } finally {
                  ErrorContext.instance().reset();
                }
              }
              
             //继续看query()和queryFromDatabase()这两个方法
            @SuppressWarnings("unchecked")
              public <E> List<E> query(MappedStatement ms, Object parameter, RowBounds rowBounds,
              ResultHandler resultHandler, CacheKey key, BoundSql boundSql) throws SQLException {
                ErrorContext.instance().resource(ms.getResource()).activity("executing a query").object(ms.getId());
                if (closed) throw new ExecutorException("Executor was closed.");
                if (queryStack == 0 && ms.isFlushCacheRequired()) {
                  clearLocalCache();
                }
                List<E> list;
                try {
                  queryStack++;
                  list = resultHandler == null ? (List<E>) localCache.getObject(key) : null;
                  if (list != null) {
                    handleLocallyCachedOutputParameters(ms, key, parameter, boundSql);
                  } else {
                    list = queryFromDatabase(ms, parameter, rowBounds, resultHandler, key, boundSql);
                  }
                } finally {
                  queryStack--;
                }
                if (queryStack == 0) {
                  for (DeferredLoad deferredLoad : deferredLoads) {
                    deferredLoad.load();
                  }
                  deferredLoads.clear(); // issue #601
                  if (configuration.getLocalCacheScope() == LocalCacheScope.STATEMENT) {
                    clearLocalCache(); // issue #482
                  }
                }
                return list;
              }
            private <E> List<E> queryFromDatabase(MappedStatement ms, Object parameter, RowBounds rowBounds,
            ResultHandler resultHandler,CacheKey key, BoundSql boundSql) throws  SQLException {
                List<E> list;
                localCache.putObject(key, EXECUTION_PLACEHOLDER);
                try {
                  list = doQuery(ms, parameter, rowBounds, resultHandler, boundSql);
                } finally {
                  localCache.removeObject(key);
                }
                localCache.putObject(key, list);
                if (ms.getStatementType() == StatementType.CALLABLE) {
                  localOutputParameterCache.putObject(key, parameter);
                }
                return list;
              }
              在这两个方法里面会为当前的查询创建一个缓存key，如果缓存中没有值，直接从数据库中读取，
              执行查询后将得到的list结果放入缓存之中。
            
            doQuery()在SimpleExecutor类中重写的方法
            public <E> List<E> doQuery(MappedStatement ms, Object parameter, RowBounds rowBounds,
            ResultHandler resultHandler, BoundSql boundSql) throws SQLException {
                Statement stmt = null;
                try {
                  Configuration configuration = ms.getConfiguration();
                  StatementHandler handler = configuration.newStatementHandler(wrapper, ms, parameter, 
                  rowBounds, resultHandler, boundSql);
                  stmt = prepareStatement(handler, ms.getStatementLog());
                  return handler.<E>query(stmt, resultHandler);
                } finally {
                  closeStatement(stmt);
                }
              }
              Executor的作用之一就是创建Statement了，创建完后又把Statement丢给StatementHandler返回List查询结果.
* 释放连接资源
#### IBatis与MyBatis区别
            
            IBatis就是MyBatis前身，有很多地方相似，在sqlMap里面区别如下
            iBatis的传入参数关键字是：parameterClass，而MyBatis是可以不写的，也可以用parameterType;
            iBatis的传出参数关键字是：resultClass， 。
            iBatis： <select id="selectDeviceByWhere" parameterClass="Map"  resultClass="BaseResultMap">  </select>
            MyBatis：<select id="selectDeviceByWhere" parameterType="Map"  resultMap="BaseResultMap">   </select> 
            IBatis是使用#  #和$   $来接受参数的。使用方法等同于MyBatis;# #=#{  },$ $=${  }.
            
            条件判断语句对于MyBatis的很简单，只要在where或者if的标签里面添加test=""就可以了，里面写判断条件。
            但是IBatis的就麻烦了许多了，它将每个都方法都进行了封装。
            例如：
            isNull：判断property字段是否是null        
            <isNull prepend="and" property="id">   </isNull>
            isEqual相当于equals，判断状态值。
            <isEqual property="state" compareValue="0">  </isEqual>
            或
            <isEqual property="state" compareProperty="nextState">  </isEqual>
            isEmpty判断参数是否为Null或者空，满足 其中一个条件则其true。
            isNotEmpty相反，当参数既不为Null也不为空是其为true。 
            
            Ibatis：
            <iterate prepend="AND" property="userNameList" open="(" close=")" conjunction="OR">  
                username=#userNameList[]#  
            </iterate>   
            Mybaits：
            <foreach item="item" collection="list" separator="," open="(" close=")" index="">
                  #{item.id}
            </foreach>

#### MyBatis中#{}和${}的区别
            
             select * from user where name = #{name};
             select * from user where name = ${name};
             解析后结果为
             select * from user where name = 'zhangsan';
             但是 #{} 和 ${} 在预编译中的处理是不一样的,#{} 在预处理时，会把参数部分用一个占位符 ? 代替，变成如下的 sql 语句：
             select * from user where name = ?;
             而 ${} 则只是简单的字符串替换，在动态解析阶段，sql语句会被解析成
             select * from user where name = 'zhangsan';
             #{}的参数替换是发生在DBMS中，而${}发生在动态解析过程中。
             ${}会导致sql注入的问题。
#### MyBatis和hibernate区别
* 灵活性：MyBatis操作灵活，使用起来比较方便。hibernate是全自动，而mybatis是半自动。hibernate可以通过对象关系模型实现对数据库的操作，通过实体对象与数据库的表进行映射来自动生成sql。而mybatis仅有基本的字段映射，对象数据以及对象实际关系仍然需要通过手写sql来实现和管理
* 可移植性：Hibernate数据库移植性远大于Mybatis。hibernate通过它强大的映射结构和hql语言，大大降低了对象与不同数据库（oracle、MySQL等）的耦合性，而mybatis由于需要手写sql，因此sql中很容易包含一些不同的数据库不兼容的函数或者语法，移植性也会随之降低很多，成本很高.
* 优化上：在sql优化上，mybatis要比hibernate方便一些，由于mybatis的sql都是写在xml里，因此优化sql比hibernate方便很多。而hibernate的sql很多都是自动生成的，无法直接维护sql；虽有hql，但功能还是不及sql强大，像报表等需求时，hql满足不了需求；hibernate虽然也支持原生sql，但开发模式上却与orm不同，需要转换思维，因此使用上不是非常方便。总之写sql的灵活度上hibernate不及mybatis。
* 二级缓存：hibernate拥有更好的二级缓存，它的二级缓存可以自行更换为第三方的二级缓存。
