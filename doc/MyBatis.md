###### iBatis就是myBatis前身，他们只有有很多地方很相似，但是在sqlMap里面已经有很多地方变动了
            
            iBatis的传入参数关键字是：parameterClass，而MyBatis是可以不写的，也可以用parameterType;
            iBatis的传出参数关键字是：resultClass，而MyBatis是resultMap。
            iBatis：    <select id="selectDeviceByWhere" parameterClass="Map"  resultClass="BaseResultMap">  </select>
            MyBatis：<select id="selectDeviceByWhere" parameterType="Map"  resultMap="BaseResultMap">   </select> 
            IBatis是使用#  #和$   $来接受参数的。使用方法等同于MyBatis;# #=#{  },$ $=${  }.
            
            条件判断语句对于MyBatis的很简单，只要在where或者if的标签里面添加test=""就可以了，里面写判断条件了。
            但是IBatis的就麻烦了许多了，它将每个都方法都进行了封装。
            例如：
            isNull：判断property字段是否是null        
            <isNull prepend="and" property="id">   </isNull>
            至于prepend就是代表着添加在动态语句前面。
            property就是被比较的属性。
            isNull：判断property字段是否不是null 
            isEqual相当于equals，判断状态值。
            <isEqual property="state" compareValue="0">  </isEqual>
            或
            <isEqual property="state" compareProperty="nextState">  </isEqual>
            isEmpty判断参数是否为Null或者空，满足其中一个条件则其true。
            isNotEmpty相反，当参数既不为Null也不为空是其为true。 
            
            Ibatis：
            <iterate prepend="AND" property="userNameList" open="(" close=")" conjunction="OR">  
                username=#userNameList[]#  
            </iterate>   
            Mybaits：
            <foreach item="item" collection="list" separator="," open="(" close=")" index="">
                  #{item.id}
            </foreach>

#### 关于sql映射文件
###### 由于我们采用mapper接口式的编程，所以sql映射文件与mapper接口类的名称要相同，并且sql映射文件中namespace的值为对应mapper接口的全类名形式。

#### MyBatis 中 #{}和 ${}的区别是什么？
            
         select * from user where name = #{name};
         select * from user where name = ${name};
         其解析之后的结果均为
         select * from user where name = 'zhangsan';
         但是 #{} 和 ${} 在预编译中的处理是不一样的。#{} 在预处理时，会把参数部分用一个占位符 ? 代替，变成如下的 sql 语句：
         select * from user where name = ?;
         而 ${} 则只是简单的字符串替换，在动态解析阶段，该 sql 语句会被解析成
         select * from user where name = 'zhangsan';
         以上，#{} 的参数替换是发生在 DBMS 中，而 ${} 则发生在动态解析过程中。
         那么，在使用过程中我们应该使用哪种方式呢？
         答案是，优先使用 #{}。因为 ${} 会导致 sql 注入的问题。

#### MyBatis 有几种分页方式？
分页方式：逻辑分页和物理分页。
逻辑分页： 使用 MyBatis 自带的 RowBounds 进行分页，它是一次性查询很多数据，然后在数据中再进行检索。
物理分页： 自己手写 SQL 分页或使用分页插件 PageHelper，去数据库查询指定条数的分页数据的形式。

## RowBounds 是一次性查询全部结果吗？为什么？
RowBounds 表面是在“所有”数据中检索数据，其实并非是一次性查询出所有数据，因为 MyBatis 是对 jdbc 的封装，在 jdbc 驱动中有一个 Fetch Size 的配置，
它规定了每次最多从数据库查询多少条数据，假如你要查询更多数据，它会在你执行 next()的时候，去查询更多的数据。就好比你去自动取款机取 10000 元，
但取款机每次最多能取 2500 元，所以你要取 4 次才能把钱取完。只是对于 jdbc 来说，当你调用 next()的时候会自动帮你完成查询工作。
这样做的好处可以有效的防止内存溢出。
Fetch Size 官方相关文档：http://t. cn/EfSE2g3

#### MyBatis 逻辑分页和物理分页的区别是什么？
· 逻辑分页是一次性查询很多数据，然后再在结果中检索分页的数据。这样做弊端是需要消耗大量的内存、有内存溢出的风险、对数据库压力较大。
· 物理分页是从数据库查询指定条数的数据，弥补了一次性全部查出的所有数据的种种缺点，比如需要大量的内存，对数据库查询压力较大等问题。

#### MyBatis 和 hibernate 的区别有哪些？
· 灵活性：MyBatis 更加灵活，自己可以写 SQL 语句，使用起来比较方便。
· 可移植性：MyBatis 有很多自己写的 SQL，因为每个数据库的 SQL 可以不相同，所以可移植性比较差。
· 学习和使用门槛：MyBatis 入门比较简单，使用门槛也更低。
· 二级缓存：hibernate 拥有更好的二级缓存，它的二级缓存可以自行更换为第三方的二级缓存。

#### MyBatis 有哪些执行器（Executor）？
MyBatis 有三种基本的Executor执行器：
· SimpleExecutor：每执行一次 update 或 select 就开启一个 Statement 对象，用完立刻关闭 Statement 对象；
· ReuseExecutor：执行 update 或 select，以 SQL 作为 key 查找 Statement 对象，存在就使用，不存在就创建，用完后不关闭 Statement 对象，
而是放置于 Map 内供下一次使用。简言之，就是重复使用 Statement 对象；
· BatchExecutor：执行 update（没有 select，jdbc 批处理不支持 select），将所有 SQL 都添加到批处理中（addBatch()），
等待统一执行（executeBatch()），它缓存了多个 Statement 对象，每个 Statement 对象都是 addBatch()完毕后，等待逐一执行 executeBatch()批处理，
与 jdbc 批处理相同。

#### MyBatis 分页插件的实现原理是什么？
分页插件的基本原理是使用 MyBatis 提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的 SQL，然后重写 SQL，根据 dialect 方言，
添加对应的物理分页语句和物理分页参数。

#### MyBatis 如何编写一个自定义插件？
自定义插件实现原理
MyBatis 自定义插件针对 MyBatis 四大对象（Executor、StatementHandler、ParameterHandler、ResultSetHandler）进行拦截：
· Executor：拦截内部执行器，它负责调用 StatementHandler 操作数据库，并把结果集通过 ResultSetHandler 进行自动映射，另外它还处理了二级缓存的操作；
· StatementHandler：拦截 SQL 语法构建的处理，它是 MyBatis 直接和数据库执行 SQL 脚本的对象，另外它也实现了 MyBatis 的一级缓存；
· ParameterHandler：拦截参数的处理；
· ResultSetHandler：拦截结果集的处理。
自定义插件实现关键
MyBatis 插件要实现 Interceptor 接口，接口包含的方法，如下：
public interface Interceptor {   
   Object intercept(Invocation invocation) throws Throwable;       
   Object plugin(Object target);    
   void setProperties(Properties properties);
}
· setProperties 方法是在 MyBatis 进行配置插件的时候可以配置自定义相关属性，即：接口实现对象的参数配置；
· plugin 方法是插件用于封装目标对象的，通过该方法我们可以返回目标对象本身，也可以返回一个它的代理，可以决定是否要进行拦截进而决定要返回一个什么样的
目标对象，官方提供了示例：return Plugin. wrap(target, this)；
· intercept 方法就是要进行拦截的时候要执行的方法。
自定义插件实现示例
官方插件实现：
@Intercepts({@Signature(type = Executor. class, method = "query",
args = {MappedStatement. class, Object. class, RowBounds. class, ResultHandler. class})})public class TestInterceptor 
implements Interceptor{
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
