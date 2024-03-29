##### 为什么要用缓存
在计算机的世界中，缓存无处不在，操作系统有操作系统的缓存，数据库也会有数据库的缓存，各种中间件如Redis也是用来充当缓存的作用，编程语言中又可以利用内存来作为缓存。
MyBatis作为一款优秀的ORM框架，也用到了缓存，本文的目的就是探究一下MyBatis的缓存是如何实现的。尤其是I/O操作，除了那种CPU密集型的系统，其余大部分的业务系统性能瓶颈最后或多或少都会出现在I/O操作上，所以为了减少磁盘的I/O次数，缓存是必不可少的，通过缓存的使用我们可以大大减少I/O操作次数，从而在一定程度上弥补了I/O操作和CPU处理速度之间的鸿沟。而在我们ORM框架中引入缓存的目的就是为了减少读取数据库的次数，从而提升查询的效率。
##### 一级缓存
前面了解了SqlSessionFactory、SqlSession、Excutor以及Mpper执行SQL过程，下面来了解下myabtis的缓存，它的缓存分为一级缓存和二级缓存。
使用MyBatis开启一次和数据库的会话，MyBatis会创建出一个SqlSession对象表示一次数据库会话，在对数据库的一次会话中，
有可能会反复地执行完全相同的查询语句，每一次查询都会去查一次数据库,为了减少资源浪费，mybaits提供了一种缓存的方式(一级缓存)。
mybatis的SQL执行最后是交给了Executor执行器来完成的，看下BaseExecutor类的源码：

                         @Override
                         public <E> List<E> query(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler) throws SQLException {
                           BoundSql boundSql = ms.getBoundSql(parameter);
                           CacheKey key = createCacheKey(ms, parameter, rowBounds, boundSql);
                           return query(ms, parameter, rowBounds, resultHandler, key, boundSql);
                        }
                         @SuppressWarnings("unchecked")
                         @Override
                         public <E> List<E> query(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, CacheKey key, BoundSql boundSql) throws                                 SQLException {
                                     ErrorContext.instance().resource(ms.getResource()).activity("executing a query").object(ms.getId());
                                     if (closed) {
                                       throw new ExecutorException("Executor was closed.");
                                     }
                                     if (queryStack == 0 && ms.isFlushCacheRequired()) {
                                       clearLocalCache();
                                     }
                                     List<E> list;
                                     try {
                                       queryStack++;
                                       list = resultHandler == null ? (List<E>) localCache.getObject(key) : null;//localCache 本地缓存
                                       if (list != null) {
                                         handleLocallyCachedOutputParameters(ms, key, parameter, boundSql);
                                       } else {
                                         list = queryFromDatabase(ms, parameter, rowBounds, resultHandler, key, boundSql);  //如果缓存没有就走DB
                                       }
                                     } finally {
                                       queryStack--;
                                     }
                                     if (queryStack == 0) {
                                       for (DeferredLoad deferredLoad : deferredLoads) {
                                         deferredLoad.load();
                                       }
                                       // issue #601
                                       deferredLoads.clear();
                                       if (configuration.getLocalCacheScope() == LocalCacheScope.STATEMENT) {
                                         // issue #482
                                         clearLocalCache();
                                       }
                                     }
                                     return list;
                               }

                        private <E> List<E> queryFromDatabase(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, CacheKey key, BoundSql                              boundSql) throws SQLException {
                         List<E> list;
                        localCache.putObject(key, EXECUTION_PLACEHOLDER);
                        try {
                          list = doQuery(ms, parameter, rowBounds, resultHandler, boundSql);
                        } finally {
                          localCache.removeObject(key);//清空现有缓存数据
                        }
                        localCache.putObject(key, list);//新的结果集存入缓存
                        if (ms.getStatementType() == StatementType.CALLABLE) {
                          localOutputParameterCache.putObject(key, parameter);
                        }
                        return list;
                      }
它的本地缓存使用的是PerpetualCache类，内部是一个HashMap作了一个封装来存数据。<br>
缓存Key的生成<br>

          CacheKey key = createCacheKey(ms, parameter, rowBounds, boundSql);
          @Override
            public CacheKey createCacheKey(MappedStatement ms, Object parameterObject, RowBounds rowBounds, BoundSql boundSql) {
              if (closed) {
                throw new ExecutorException("Executor was closed.");
              }
              CacheKey cacheKey = new CacheKey();
              cacheKey.update(ms.getId());
              cacheKey.update(Integer.valueOf(rowBounds.getOffset()));
              cacheKey.update(Integer.valueOf(rowBounds.getLimit()));
              cacheKey.update(boundSql.getSql());
              List<ParameterMapping> parameterMappings = boundSql.getParameterMappings();
              TypeHandlerRegistry typeHandlerRegistry = ms.getConfiguration().getTypeHandlerRegistry();
              // mimic DefaultParameterHandler logic
              for (int i = 0; i < parameterMappings.size(); i++) {
                ParameterMapping parameterMapping = parameterMappings.get(i);
                if (parameterMapping.getMode() != ParameterMode.OUT) {
                  Object value;
                  String propertyName = parameterMapping.getProperty();
                  if (boundSql.hasAdditionalParameter(propertyName)) {
                    value = boundSql.getAdditionalParameter(propertyName);
                  } else if (parameterObject == null) {
                    value = null;
                  } else if (typeHandlerRegistry.hasTypeHandler(parameterObject.getClass())) {
                    value = parameterObject;
                  } else {
                    MetaObject metaObject = configuration.newMetaObject(parameterObject);
                    value = metaObject.getValue(propertyName);
                  }
                  cacheKey.update(value);
                }
              }
              if (configuration.getEnvironment() != null) {
                // issue #176
                cacheKey.update(configuration.getEnvironment().getId());
              }
              return cacheKey;
            }
通过statementId,params,rowBounds，BoundSql来构建一个key值，根据这个key值去缓存Cache中取出对应缓存结果。
##### 一级缓存的生命周期
* 比如要执行一个查询操作时，Mybatis会创建一个新的SqlSession对象，SqlSession对象找到具体的Executor，
Executor持有一个PerpetualCache对象；当查询结束(会话结束)时，SqlSession、Executor、PerpetualCache对象占有的资源一并释放掉。
* 如果SqlSession调用了close()方法，会释放掉一级缓存PerpetualCache对象，一级缓存将不可用。
* 如果SqlSession调用了clearCache()，会清空PerpetualCache对象中的数据，但是该对象仍可使用。
* SqlSession中执行了任何一个update操作(update()、delete()、insert()) ，都会清空PerpetualCache对象的数据，但是该对象可以继续使用。
##### 一级缓存失效情况         

          注意：一级缓存为sqlSession级别的缓存，默认开启的，不能关闭。一级缓存失效的四种情况：
          1）sqlSession不同，缓存失效。
          2）sqlSession相同，查询条件不同，缓存失效，因为缓存中可能还没有相关数据。
          3）sqlSession相同，在两次查询期间，执行了增删改操作，缓存失效。
          4）sqlSession相同，但是手动清空了一级缓存，缓存失效。
          清除缓存情况：
          1、就是获取缓存之前会先进行判断用户是否配置了flushCache=true属性（参考一级缓存的创建代码截图），
          如果配置了则会清除一级缓存。
          2、MyBatis全局配置属性localCacheScope配置为Statement时，那么完成一次查询就会清除缓存。
          3、在执行commit，rollback，update方法时会清空一级缓存
##### 二级缓存
Mybatis默认对二级缓存是关闭的，一级缓存默认开启，如果需要开启只需在mapper上加入配置就好了。<br>
Executor是执行查询的最终接口，它有两个实现类一个是BaseExecutor另外一个是CachingExecutor。CachingExecutor(二级缓存查询)<br>
一级缓存因为只能在同一个SqlSession中共享，所以会存在一个问题，在分布式或者多线程的环境下，不同会话之间对于相同的数据可能会产生不同的结果，因为跨会话修改了数据是不能互相感知的，所以就有可能存在脏数据的问题，正因为一级缓存存在这种不足，需要一种作用域更大的缓存，这就是二级缓存。<br>
CachingExecutor实现类里面的query查询方法：<br>

            @Override
            public  List query(MappedStatement ms, Object parameterObject, RowBounds rowBounds, ResultHandler resultHandler, CacheKey key, BoundSql boundSql)
                throws SQLException {
              Cache cache = ms.getCache();//二级缓存对象
              if (cache != null) {
                flushCacheIfRequired(ms);
                if (ms.isUseCache() && resultHandler == null) {
                  ensureNoOutParams(ms, parameterObject, boundSql);
                  @SuppressWarnings("unchecked")
                  List list = (List) tcm.getObject(cache, key);//从缓存中读取
                  if (list == null) {
                    //这段走到一级缓存或者DB
                    list = delegate. query(ms, parameterObject, rowBounds, resultHandler, key, boundSql);
                    tcm.putObject(cache, key, list); // issue #578 and #116  //数据放入缓存
                  }
                  return list;
                }
              }
              return delegate. query(ms, parameterObject, rowBounds, resultHandler, key, boundSql);
            }

一个事务方法运行时，数据查询出来，缓存在一级缓存了，但是没有到二级缓存，当事务提交后(sqlSession.commit())，数据才放到二级缓存。查询的顺序是，先查二级缓存再查一级缓存然后才去数据库查询。

一级缓存作用域是SqlSession级别，所以它存储的SqlSession中的BaseExecutor之中，但是二级缓存目的要实现作用范围更广，所以要实现跨会话共享，MyBatis二级缓存的作用域是namespace，专门用了一个装饰器来维护，这就是：CachingExecutor。

          二级缓存相关的配置有三个地方：
          1、mybatis-config中有一个全局配置属性，这个不配置也行，因为默认就是true。
          <setting name="cacheEnabled" value="true"/>
          想详细了解mybatis-config的可以点击这里。
          2、在Mapper映射文件内需要配置缓存标签：
          <cache/>
          或
          <cache-ref namespace="com.lonelyWolf.mybatis.mapper.UserAddressMapper"/>
          想详细了解Mapper映射的所有标签属性配置可以点击这里。
          3、在select查询语句标签上配置useCache属性，如下：
          <select id="selectUserAndJob" resultMap="JobResultMap2" useCache="true">
              select * from lw_user
          </select>
          以上配置第1点是默认开启的，也就是说我们只要配置第2点就可以打开二级缓存了，
          而第3点是当我们需要针对某一条语句来配置二级缓存时候则可以使用。
          1、需要commit事务之后才会生效
          2、如果使用的是默认缓存，那么结果集对象需要实现序列化接口(Serializable)
          如果不实现序列化接口则会报如下错误
          
          * 二级缓存工作机制(？)
          1）一个会话，查询一条数据，该数据会放在当前会话的一级缓存中。
          2）如果当前会话关闭，对应的一级缓存会被保存到二级缓存中，新的会话查询信息，就可以参照二级缓存。
          3）不同namespace查询出的数据会放在自己对应的缓存中。
          注意：查出的数据都会默认放在一级缓存中，只有会话提交或关闭后，一级缓存的数据才会被转移到二级缓存中。
          
          * 需要注意的是在事务提交之前，并不会真正存储到二级缓存，而是先存储到一个临时属性，
          * 等事务提交之后才会真正存储到二级缓存。（？）
          Mybatis缓存包装汇总：
          PerpetualCache	缓存默认实现类	-	基本功能，默认携带
          LruCache	LRU淘汰策略缓存（默认淘汰策略）	当缓存达到上限，删除最近最少使用缓存	eviction=“LRU”
          FifoCache	FIFO淘汰策略缓存	当缓存达到上限，删除最先入队的缓存	eviction=“FIFO”
          SoftCache	JVM软引用淘汰策略缓存	基于JVM的SoftReference对象	eviction=“SOFT”
          WeakCache	JVM弱引用淘汰策略缓存	基于JVM的WeakReference对象	eviction=“WEAK”
          LoggingCache	带日志功能缓存	输出缓存相关日志信息	基本功能，默认包装
          SynchronizedCache	同步缓存	基于synchronized关键字实现，用来解决并发问题	基本功能，默认包装
          BlockingCache	阻塞缓存	get/put操作时会加锁，防止并发，基于Java重入锁实现	blocking=true
          SerializedCache	支持序列化的缓存	通过序列化和反序列化来存储和读取缓存	readOnly=false(默认)
          ScheduledCache	定时调度缓存	操作缓存时如果缓存已经达到了设置的最长缓存时间时会移除缓存	flushInterval属性不为空
          TransactionalCache	事务缓存	在TransactionalCacheManager中用于维护缓存map的value值	
* 所有的update操作(insert,delete,uptede)都会触发缓存的刷新，从而导致二级缓存失效，所以二级缓存适合在读多写少的场景中开启。
* 二级缓存针对的是同一个namespace，所以建议是在单表操作的Mapper中使用，或者是在相关表的Mapper文件中共享同一个缓存。
##### 自定义缓存
一级缓存可能存在脏读情况，那么二级缓存是否也可能存在呢？是的，默认的二级缓存也是存储在本地缓存，对于微服务下是可能出现脏读的情况的，这时可能会需要自定义缓存，比如利用redis来存储缓存，而不是存储在本地内存当中。

          MyBatis官方也提供了第三方缓存的支持引入pom文件：
          <dependency>
              <groupId>org.mybatis.caches</groupId>
              <artifactId>mybatis-redis</artifactId>
              <version>1.0.0-beta2</version>
          </dependency>
          然后缓存配置如下：
          <cache type="org.mybatis.caches.redis.RedisCache"></cache>
          然后在默认的resource路径下新建一个redis.properties文件：
          host=localhost
          port=6379
#### 延迟加载原理
延迟加载原理：调用的时候触发加载，不是在初始化的时候就加载信息。MyBatis支持延迟加载，设置lazyLoadingEnabled=true即可。
          
          比如：a.getB().getName()，发现a.getB()的值为null，此时会单独触发事件，将保存好的关联B对象的SQL查询出来，
          然后再调用a.setB(b)，这时再调用a.getB().getName()就有值了。
