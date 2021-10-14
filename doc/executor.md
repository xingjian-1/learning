##### 通用Mapper
tk.mybatis包提供通用的增删改查的方法

            <!-- 通用Mapper依赖 -->
            <dependency>
                <groupId>tk.mybatis</groupId>
                <artifactId>mapper</artifactId>
                <version>4.0.4</version>
            </dependency>
* 更新：

updateByPrimaryKey(T var1) 根据主键更新数据，更新NULL的字段，<br>
updateByPrimaryKeySelective(T var1)根据主键更新有值的数据，<br>
updateByCondition(@Param("record") T var1, @Param("condition") Object var2) 根据Condition条件更新实体`record`包含的全部属性，null值会被更新，<br>
updateByConditionSelective(@Param("record") T var1, @Param("condition") Object var2) 根据Condition条件更新实体`record`包含的不是null的属性值，<br>
updateByExampleSelective(@Param("record") T var1, @Param("example") Object var2)根据Example条件更新实体`record`包含的不是null的属性值，<br>
updateByExample(@Param("record") T var1, @Param("example") Object var2)根据Example条件更新实体`record`包含的全部属性，null值会被更新，<br>
updateListByPrimaryKey(List<? extends T> recordList);根据主键批量修改。<br>
<br>
根据指定条件更新指定字段的情况上面几种方法可能都不满足，就只能自己写sql了。<br>

           updateStatusByPrimaryKey(InsureOrgSchemesInfo insureOrgSchemesInfo)；
           <update id="updateStatusByPrimaryKey" parameterType="com.xtr.manager.api.domain.insure.InsureOrgSchemesInfo">
                   update INSURE_ORG_SCHEMES_INFO a
                   <set>
                       <if test="state != null and state != ''">
                           a.state = #{state}
                       </if>
                   </set>
                   where a.id = #{id}
           </update>

* 插入

insertList(List<? extends T> var1)：批量新增<br>
insert(T var1) 所有的字段都会添加一遍，即使有的字段没有值<br>
insertSelective(T var1) 只给有值的字段赋值（会对传进来的值做非空判断）<br>
insert和insertSelective在数据库中的效果是一样的，只是sql语句不同。<br>
insertUseGeneratedKeys()：<br>
官网文档：https://dev.mysql.com/doc/refman/5.6/en/insert-on-duplicate.html<br>

* 删除

delete(T var1)；<br>

         DELETE FROM db_test.t_test_table 
         <where>
          <if test="id != null"> 
           AND id = #{id}
          </if>
          <if test="col != null"> 
          AND col = #{col}
          </if>
         </where>
deleteByPrimaryKey(Object var1):

        DELETE FROM db_test.t_test_table 
        <where> 
         AND id = #{id}
        </where>
deleteByExample(Object var1) 根据条件删除<br>
Example example = new Example(User.class);<br>
Example.Criteria criteria = example.createCriteria();<br>
criteria.andEqualTo("name","王小二");//相当于 where name = "王小二"<br>
deleteByExample(example );<br>

* 查询

select(T var1) 根据条件查询，作非空判断。<br>
selectCount(T var1)根据条件查询 查询行数<br>
selectAll() 查询全部<br>
selectByExample(Object var1)根据Example条件查询<br>
selectCountByExample(Object var1)根据Example条件查询行数<br>
selectOne(T var1)根据条件查询一个<br>
selectOneByExample(Object var1)<br>
selectByExampleAndRowBounds(Object var1, RowBounds var2)根据example条件和RowBounds进行分页查询<br>
selectByRowBounds(T var1, RowBounds var2)根据实体条件和RowBounds进行分页查询<br>
selectByPrimaryKey(Object var1) 根据主键查询<br>
##### 执行器三种实现
在mybatis-3.4.6中执行器分为：简单执行器(SimpleExecutor)、可重用执行器(ReuseExecutor)、批处理执行器(BatchExecutor),都继承了抽象类BaseExecutor，
BaseExecutor实现了Executor接口。执行器的职责包括：执行、事务、缓存。

##### 简单执行器
SimpleExecutor是执行器的默认实现，利用StatementHandler完成。每次调用执行方法都会构建一个StatementHandler，并预设参数，然后执行。

      SimpleExecutor executor = new SimpleExecutor(configuration, jdbcTransaction);
      List<Object> list = executor.doQuery(ms, 10, DEFAULT,NO_RESULT_HANDLER, ms.getBoundSql(10));

##### 可重用执行器
ReuseExecutor可重用执行器，可重复使用JDBC中Statement，减少预编译次数。该执行器在执行SQL时会把Statement缓存起来，如果下次碰到相同的SQL，就会取出来使用
##### 批处理执行器
BatchExecutor 批处理执行器，每次的执行操作 不会立即提交到数据库，而是把对应的Statement对象填充好参数之后暂存起来。调用doFlushStatements的时候一次性提交到数据库，可用于批处理插入的场景。
ReuseExecutor只要SQL相同就可以共用。但BatchExecutor 除了操作的sql相同还必须这些sql执行的顺序是连续的。<br>
为保证批处理的执行顺序，必须与调用方执行SQL顺序相同。比如调用方依次发起1、2、3、4个操作，其中操作1和操作4是相同的Update语句，假如操作4合并到操作1，其就会先于2、3执行。<br>
这显然是会出问题的。假如操作4用来修改操作3新增的记录，它就修改不了。<br>
##### 基础执行器
BaseExecutor(基础执行器)是一个抽象类。三种执行器都是继承自它，并实现它的 doQuery与doUpdate方法，用于具体的执行。
##### 一级缓存
执行器的使用从query与update方法进入，然后BaseExecute 在其中加入一级缓存逻辑，如：创建缓存Key，获取缓存、清空缓存、填充缓存等。
如果有缓存直接从缓存中取数据，如果没有缓存则调用SimpleExecutor中的doQuery方法查询数据库。
##### 事务
关于事务基础执行器提供了，commit与rollback方法。除了常规JDBC提交回滚外，还包括缓存的清空与批处理的提交与回滚操作。
##### 会话与执行器
一般开发， 不会直接使用执行器，而是通过会话来使用，创建一个SqlSession，然后SqlSession去调Executor。
在会话中会包含一个唯一的执行器，并且一个执行器，只能被一个会话使用，二者是1对1的关系
在开启会话时， 可通过传递ExecutorType 参数来指定具体的执行器类型。其值有SIMPLE, REUSE, BATCH三种，分别对应三种执行器。

      <setting name="defaultExecutorType" value="SIMPLE|REUSE|BATCH"/>

如果把defaultExecutorType设置成其中一个SIMPLE，那么它的执行流程就是：发起查询操作(select)-创建SqlSession会话对象-BaseExecutor执行器里面的query方法-如果缓存中没数据就调SimpleExecutor中的doQuery方法。

##### 二级缓存执行器
CacheExecutor是二级缓存执行器，里面包含了二级缓存的逻辑。这里面的设计用到了装饰器模式，对一个执行器进行装饰，从而嵌入二级缓存功能

      Executor delegate = new SimpleExecutor(configuration, jdbcTransaction);
      CachingExecutor executor = new CachingExecutor(delegate);
      Object result=executor.query(ms, 10, DEFAULT, NO_RESULT_HANDLER);
代码中delegate是一个简单执行器，它被CachingExecutor所装饰。当调用query方法时先检查有没有命中二级缓存，如果没有再交给delegate继续完成查询逻辑。<br>
在默认情况下二级缓存是开启的，可通过以下参数关闭：

      <setting name="cacheEnabled" value="true"/>
执行流程：创建SqlSession会话-CachingExecutor-缓存中如果没有数据就调BaseExecutor-然后找到具体的执行器实现(SimpleExecutor/ReuseExecutor/BatchExecutor)。
##### 执行器的构建
执行器的构建在打开会话时，基于Configuration中的newExecutor方法实现。创建流程：会话打开-基于指定类型创建具体执行器-创建缓存执行器-如果有插件的话可能会对缓存执行器进行封装。
##### 总结
执行器有三种实现分别是，简单、重用、批处理执行器，三种执行之上是基础执行器，用于实现一级缓存与事务的功能。执行器与会话关系是1比1的关系，每个会话都对应一个唯一的执行器
缓存执行器采用了装饰者设计模式，从而嵌入了二级缓存功能。<br>

src="/img/Executor.jpg" width="50%" hight="30%">
