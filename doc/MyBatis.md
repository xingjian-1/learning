#### 框架概述
`简介`：MyBatis是一款优秀的持久层框架，它支持定制化SQL、存储过程以及高级映射。避免了JDBC代码和手动设置参数以及获取结果集。MyBatis使用XML或注解来配置和映射原生信息，将接口和Java的POJO(Plain Ordinary Java Object,普通的Java对象)映射成数据库中的记录。<br>
`特点`：轻量级、解耦(将业务逻辑和数据访问逻辑分离，使系统的设计结构更清晰，易维护，更易单元测试)、提供xml标签，支持编写动态sql<br>

            MyBatis最强大的特性之一就是它的动态语句功能。如果以前有使用JDBC或者类似框架的经历，
            就会明白把SQL语句条件连接在一起是多么的痛苦，要确保不能忘记空格或者不要在columns列后面省略一个逗号等。
            动态语句解决掉了这些问题。
`结构`:
* API接口层：提供一些API接口，通过这些API接口来操纵数据库,接口层收到调用请求会调用数据处理层来完成具体的数据处理。
* 数据处理层：负责具体的SQL查找、SQL解析、SQL执行和执行结果映射处理等,主要目的是根据调用的请求完成一次数据库操作。
* 基础支撑层：负责最基础的功能支撑，包括连接管理、事务管理、配置加载和缓存处理，这些都是共用的功能，将他们抽取出来作为最基础的组件，为上层的数据处理层提供最基础的支撑。           
#### 工作原理      
* 加载配置：加载配置文件里面数据库连接信息和mapper里面的那些映射文件，生成一个个的MappedStatement对象，然后调用方法addMappedStatement()方法以Id为主键保存在了configuration中一个map变量里面。

            <?xml version="1.0" encoding="UTF-8" ?>
            <!DOCTYPE configuration
            PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-config.dtd">
            <configuration>
                <environments default="development">
                    <environment id="development">
                        <transactionManager type="JDBC" />
                        <dataSource type="POOLED">
                            <property name="driver" value="com.mysql.jdbc.Driver" />
                            <property name="url"
                                value="jdbc:mysql://localhost:3306/test?characterEncoding=utf-8" />
                            <property name="username" value="root" />
                            <property name="password" value="123456" />
                        </dataSource>
                    </environment>
                </environments>
                <mappers>
                   <mapper  resource="sqlMapper/userMapper.xml"/>
                </mappers>
            </configuration>
            
            public MappedStatement addMappedStatement(
                  String id,
                  SqlSource sqlSource,
                  StatementType statementType,
                  SqlCommandType sqlCommandType,
                  Integer fetchSize,
                  Integer timeout,
                  String parameterMap,
                  Class<?> parameterType,
                  String resultMap,
                  Class<?> resultType,
                  ResultSetType resultSetType,
                  boolean flushCache,
                  boolean useCache,
                  boolean resultOrdered,
                  KeyGenerator keyGenerator,
                  String keyProperty,
                  String keyColumn,
                  String databaseId,
                  LanguageDriver lang,
                  String resultSets) {
                if (unresolvedCacheRef) throw new IncompleteElementException("Cache-ref not yet resolved");
                id = applyCurrentNamespace(id, false);
                boolean isSelect = sqlCommandType == SqlCommandType.SELECT;
                MappedStatement.Builder statementBuilder = new MappedStatement.Builder(configuration, id,
                sqlSource, sqlCommandType);
                statementBuilder.resource(resource);
                statementBuilder.fetchSize(fetchSize);
                statementBuilder.statementType(statementType);
                statementBuilder.keyGenerator(keyGenerator);
                statementBuilder.keyProperty(keyProperty);
                statementBuilder.keyColumn(keyColumn);
                statementBuilder.databaseId(databaseId);
                statementBuilder.lang(lang);
                statementBuilder.resultOrdered(resultOrdered);
                statementBuilder.resulSets(resultSets);
                setStatementTimeout(timeout, statementBuilder);
                setStatementParameterMap(parameterMap, parameterType, statementBuilder);
                setStatementResultMap(resultMap, resultType, resultSetType, statementBuilder);
                setStatementCache(isSelect, flushCache, useCache, currentCache, statementBuilder);
                MappedStatement statement = statementBuilder.build();
                configuration.addMappedStatement(statement);
                return statement;
              }
* 创建sqlSessionFactory
上面的一切操作都是为了生成一个sqlSessionFactory

            public SqlSessionFactory build(InputStream inputStream, String environment, Properties properties) {
                try {
                  XMLConfigBuilder parser = new XMLConfigBuilder(inputStream, environment, properties);
                  return build(parser.parse());
                } catch (Exception e) {
                  throw ExceptionFactory.wrapException("Error building SqlSession.", e);
                } finally {
                  ErrorContext.instance().reset();
                  try {
                    inputStream.close();
                  } catch (IOException e) {
                    // Intentionally ignore. Prefer previous error.
                  }
                }
              }

              public SqlSessionFactory build(Configuration config) {
                return new DefaultSqlSessionFactory(config);
              }
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
              
             //再继续看query()和queryFromDatabase()这两个方法
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
#### MyBatis执行器Executor
三种基本的Executor执行器：
* SimpleExecutor：每执行一次 update或select就开启一个Statement对象，用完立刻关闭Statement对象；
* ReuseExecutor：执行update或select，以SQL作为key查找Statement对象，存在就使用，不存在就创建，用完后不关闭Statement对象，而是放置于Map内供下一次使用。简言之，就是重复使用Statement对象；
* BatchExecutor：执行update（没有select，jdbc批处理不支持select），将所有SQL都添加到批处理中（addBatch()），等待统一执行（executeBatch()），它缓存了多个Statement对象，每个Statement对象都是addBatch()完毕后，等待逐一执行executeBatch()批处理，与jdbc批处理相同。
#### MyBatis如何编写一个自定义插件
自定义插件实现原理
MyBatis自定义插件针对MyBatis四大对象（Executor、StatementHandler、ParameterHandler、ResultSetHandler）进行拦截：
* Executor：拦截内部执行器，它负责调用StatementHandler 操作数据库，并把结果集通过ResultSetHandler进行自动映射，另外它还处理了二级缓存的操作；
* StatementHandler：拦截SQL语法构建的处理，它是MyBatis直接和数据库执行SQL脚本的对象，另外它也实现了MyBatis的一级缓存；
* ParameterHandler：拦截参数的处理；
* ResultSetHandler：拦截结果集的处理。
 
                        MyBatis 插件要实现 Interceptor 接口，接口包含的方法，如下：
                        public interface Interceptor {   
                           Object intercept(Invocation invocation) throws Throwable;       
                           Object plugin(Object target);    
                           void setProperties(Properties properties);
                        }
* setProperties 方法是在MyBatis进行配置插件的时候可以配置自定义相关属性，即：接口实现对象的参数配置；
* plugin 方法是插件用于封装目标对象的，通过该方法我们可以返回目标对象本身，也可以返回一个它的代理，可以决定是否要进行拦截进而决定要返回一个什么样的
目标对象，官方提供了示例：return Plugin. wrap(target, this)；
* intercept 方法就是要进行拦截的时候要执行的方法。

                        //插件实现：
                        @Intercepts({@Signature(type = Executor. class, method = "query",
                        args = {MappedStatement. class, Object. class, RowBounds. class, ResultHandler. class})})
                        public class TestInterceptor implements Interceptor{
                           public Object intercept(Invocation invocation) throws Throwable {
                             Object target = invocation. getTarget(); //被代理对象
                             Method method = invocation. getMethod(); //代理方法
                             Object[] args = invocation. getArgs(); //方法参数
                             // do something . . . . . .  方法拦截前执行代码块
                             Object result = invocation. proceed();
                             // do something . . . . . . . 方法拦截后执行代码块
                             return result;  }
                           public Object plugin(Object target) {
                             return Plugin. wrap(target, this);
                           }
                        }
#### MyBatis分页方式
* 逻辑分页：使用MyBatis自带的RowBounds进行分页，一次性查询很多数据，然后在结果中检索分页的数据。这样弊端是需要消耗大量的内存、有内存溢出的风险、对数据库压力较大。
* 物理分页：自己手写SQL分页或使用分页插件PageHelper，从数据库查询指定条数的数据，弥补了一次性全部查出的所有数据的种种缺点，比如需要大量的内存，对数据库查询压力较大等问题。
* 原理：分页插件的基本原理是使用 MyBatis 提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的 SQL，然后重写 SQL，根据dialect方言，
添加对应的物理分页语句和物理分页参数。

            RowBounds不是一次性查询全部结果，因为MyBatis是对jdbc的封装，在jdbc驱动中有一个Fetch Size的配置，
            它规定了每次最多从数据库查询多少条数据，假如你要查询更多数据，它会在你执行next()的时候，去查询更多的数据。

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
* 灵活性：MyBatis更加灵活，自己可以写SQL语句，使用起来比较方便。hibernate是全自动，而mybatis是半自动。hibernate完全可以通过对象关系模型实现对数据库的操作，通过实体对象与数据库的表进行映射来自动生成sql。而mybatis仅有基本的字段映射，对象数据以及对象实际关系仍然需要通过手写sql来实现和管理
* 可移植性：Hibernate数据库移植性远大于Mybatis。hibernate通过它强大的映射结构和hql语言，大大降低了对象与不同数据库（oracle、MySQL等）的耦合性，而mybatis由于需要手写sql，因此sql中很容易包含一些不同的数据库不兼容的函数或者语法，移植性也会随之降低很多，成本很高.
* 优化上：在sql优化上，mybatis要比hibernate方便一些，由于mybatis的sql都是写在xml里，因此优化sql比hibernate方便很多。而hibernate的sql很多都是自动生成的，无法直接维护sql；虽有hql，但功能还是不及sql强大，见到报表等变态需求时，hql也歇菜，也就是说hql是有局限的；hibernate虽然也支持原生sql，但开发模式上却与orm不同，需要转换思维，因此使用上不是非常方便。总之写sql的灵活度上hibernate不及mybatis
* 学习和使用门槛：MyBatis入门比较简单，使用门槛也更低。
* 二级缓存：hibernate拥有更好的二级缓存，它的二级缓存可以自行更换为第三方的二级缓存。
