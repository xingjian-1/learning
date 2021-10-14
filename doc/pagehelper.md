##### MyBatis分页方式
* 逻辑分页
使用MyBatis自带的RowBounds进行分页，一次性查询很多数据，然后在结果中检索分页的数据。这样弊端是需要消耗大量的内存、有内存溢出的风险、对数据库压力较大。
RowBounds不是一次性查询全部结果，因为MyBatis是对jdbc的封装，在jdbc驱动中有一个Fetch Size的配置，它规定了每次最多从数据库查询多少条数据，假如你要查询更多数据，它会在你执行next()的时候，去查询更多的数据。
* 物理分页
自己手写SQL分页或使用分页插件PageHelper，从数据库查询指定条数的数据。
* 原理
分页插件的基本原理是使用MyBatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的SQL，然后重写SQL，根据dialect方言，添加对应的物理分页语句和物理分页参数。

##### 分页插件(PageHelper) 

            maven依赖:
            <dependency>  
                <groupId>com.github.pagehelper</groupId>  
                <artifactId>pagehelper</artifactId>  
                <version>4.1.6</version>  
            </dependency
分页

           SqlSession sqlSession = sessionFactory.openSession();
           UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
           PageHelper.startPage(1,10,true);  //第一页 每页显示10条
           Page<User> page=userMapper.findUserAll();
不分页

           PageHelper.startPage(1,-1,true);
查询总条数

           PageInfo<User>  info=new PageInfo<>(userMapper.findUserAll());
