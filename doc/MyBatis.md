#### 简介
MyBatis是一款优秀的持久层框架，它支持定制化SQL、存储过程以及高级映射。MyBatis避免了几乎所有的JDBC代码和手动设置参数以及获取结果集。MyBatis可以使用简单的XML或注解来配置和映射原生信息，将接口和Java的POJOs(Plain Ordinary Java Object,普通的Java对象)映射成数据库中的记录。
##### 特点
轻量级、解耦(将业务逻辑和数据访问逻辑分离，使系统的设计更清晰，更易维护，更易单元测试。sql和代码的分离，提高了可维护性)、提供xml标签，支持编写动态sql
##### 结构
* (1)API接口层：提供给外部使用的接口API，开发人员通过这些本地API来操纵数据库。接口层一接收到调用请求就会调用数据处理层来完成具体的数据处理。
* (2)数据处理层：负责具体的SQL查找、SQL解析、SQL执行和执行结果映射处理等。它主要的目的是根据调用的请求完成一次数据库操作。
* (3)基础支撑层：负责最基础的功能支撑，包括连接管理、事务管理、配置加载和缓存处理，这些都是共用的东西，将他们抽取出来作为最基础的组件。为上层的数据处理层提供最基础的支撑
#### 框架解析
* (1)加载配置：配置来源于两个地方，一处是配置文件，一处是Java代码的注解，将SQL的配置信息加载成为一个个MappedStatement对象（包括了传入参数映射配置、执行的SQL语句、结果映射配置），存储在内存中。
* (2)SQL解析：当API接口层接收到调用请求时，会接收到传入SQL的ID和传入对象（可以是Map、JavaBean或者基本数据类型），Mybatis会根据SQL的ID找到对应的MappedStatement，然后根据传入参数对象对MappedStatement进行解析，解析后可以得到最终要执行的SQL语句和参数。
* (3)SQL执行：将最终得到的SQL和参数拿到数据库进行执行，得到操作数据库的结果。
* (4)结果映射：将操作数据库的结果按照映射的配置进行转换，可以转换成HashMap、JavaBean或者基本数据类型，并将最终结果返回。

      MyBatis最强大的特性之一就是它的动态语句功能。如果以前有使用JDBC或者类似框架的经历，就会明白把SQL语句条件连接在一起是多么的痛苦，要确保不能忘记空格或者不要在columns列后面省                   略一个逗号等。动态语句能够完全解决掉这些问题。
      MyBatis能通过在任何映射SQL语句中使用强大的动态SQL来改进这些状况。动态SQL元素对于任何使用过JSTL或者类似于XML之类的文本处理器的人来说，都是非常熟悉的。
      MyBatis使用了基于强大的OGNL表达式来消除了大部分元素。
#### 工作流程
            (1)加载配置并初始化 触发条件：加载配置文件
            处理过程：将SQL的配置信息加载成为一个个MappedStatement对象（包括了传入参数映射配置、执行的SQL语句、结果映射配置），存储在内存中。
            (2)接收调用请求 触发条件：调用Mybatis提供的API
            传入参数：为SQL的ID和传入参数对象
            处理过程：将请求传递给下层的请求处理层进行处理。
            (3)处理操作请求 触发条件：API接口层传递请求过来
            传入参数：为SQL的ID和传入参数对象
            处理过程：
            (A)根据SQL的ID查找对应的MappedStatement对象。
            (B)根据传入参数对象解析MappedStatement对象，得到最终要执行的SQL和执行传入参数。
            (C)获取数据库连接，根据得到的最终SQL语句和执行传入参数到数据库执行，并得到执行结果。
            (D)根据MappedStatement对象中的结果映射配置对得到的执行结果进行转换处理，并得到最终的处理结果。
            (E)释放连接资源。
            (4)返回处理结果将最终的处理结果返回。
#### IBatis与MyBatis区别
            
            IBatis就是MyBatis前身，有很多地方相似，在sqlMap里面区别如下
            iBatis的传入参数关键字是：parameterClass，而MyBatis是可以不写的，也可以用parameterType;
            iBatis的传出参数关键字是：resultClass，而MyBatis是resultMap。
            iBatis： <select id="selectDeviceByWhere" parameterClass="Map"  resultClass="BaseResultMap">  </select>
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
由于我们采用mapper接口式的编程，所以sql映射文件与mapper接口类的名称要相同，并且sql映射文件中namespace的值为对应mapper接口的全类名形式。

#### MyBatis中#{}和${}的区别
            
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

#### RowBounds是一次性查询全部结果吗
RowBounds 表面是在“所有”数据中检索数据，其实并非是一次性查询出所有数据，因为MyBatis是对jdbc的封装，在jdbc驱动中有一个Fetch Size的配置，
它规定了每次最多从数据库查询多少条数据，假如你要查询更多数据，它会在你执行next()的时候，去查询更多的数据。就好比你去自动取款机取10000元，
但取款机每次最多能取2500元，所以你要取4次才能把钱取完。只是对于jdbc来说，当你调用next()的时候会自动帮你完成查询工作。
这样做的好处可以有效的防止内存溢出。

#### MyBatis分页方式
* 逻辑分页：使用MyBatis自带的RowBounds进行分页，一次性查询很多数据，然后再在结果中检索分页的数据。这样做弊端是需要消耗大量的内存、有内存溢出的风险、对数据库压力较大。
* 物理分页：自己手写SQL分页或使用分页插件PageHelper，从数据库查询指定条数的数据，弥补了一次性全部查出的所有数据的种种缺点，比如需要大量的内存，对数据库查询压力较大等问题。
* 原理：分页插件的基本原理是使用 MyBatis 提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的 SQL，然后重写 SQL，根据dialect方言，
添加对应的物理分页语句和物理分页参数。

#### MyBatis和hibernate区别
* 灵活性：MyBatis更加灵活，自己可以写SQL语句，使用起来比较方便。hibernate是全自动，而mybatis是半自动。
hibernate完全可以通过对象关系模型实现对数据库的操作，通过实体对象与数据库的表进行映射来自动生成sql。而mybatis仅有基本的字段映射，对象数据以及对象实际关系仍然需要通过手写sql来实现和管理
* 可移植性：Hibernate数据库移植性远大于Mybatis。hibernate通过它强大的映射结构和hql语言，大大降低了对象与不同数据库（oracle、MySQL等）的耦合性，而mybatis由于需要手写sql，因此sql中很容易包含一些不同的数据库不兼容的函数或者语法，移植性也会随之降低很多，成本很高.
* 优化上：在sql优化上，mybatis要比hibernate方便一些，由于mybatis的sql都是写在xml里，因此优化sql比hibernate方便很多。而hibernate的sql很多都是自动生成的，无法直接维护sql；虽有hql，但功能还是不及sql强大，见到报表等变态需求时，hql也歇菜，也就是说hql是有局限的；hibernate虽然也支持原生sql，但开发模式上却与orm不同，需要转换思维，因此使用上不是非常方便。总之写sql的灵活度上hibernate不及mybatis
* 学习和使用门槛：MyBatis入门比较简单，使用门槛也更低。
* 二级缓存：hibernate拥有更好的二级缓存，它的二级缓存可以自行更换为第三方的二级缓存。

#### MyBatis有哪些执行器Executor
MyBatis有三种基本的Executor执行器：
* SimpleExecutor：每执行一次 update或select就开启一个Statement 对象，用完立刻关闭Statement对象；
* ReuseExecutor：执行update或select，以 SQL 作为key查找Statement对象，存在就使用，不存在就创建，用完后不关闭Statement对象，
而是放置于Map内供下一次使用。简言之，就是重复使用Statement对象；
* BatchExecutor：执行update（没有 select，jdbc批处理不支持select），将所有SQL都添加到批处理中（addBatch()），
等待统一执行（executeBatch()），它缓存了多个Statement对象，每个Statement对象都是addBatch()完毕后，等待逐一执行executeBatch()批处理，
与jdbc批处理相同。
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
