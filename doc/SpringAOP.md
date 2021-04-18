#### AOP
为什么会有面向切面编程（AOP）？我们知道Java是一个面向对象(OOP)的语言，但它有一些弊端，比如当我们需要为多个不具有继承关系的对象引入一个公共行为。例如日志，权限验证，事务等功能时，只能在在每个对象里引用公共行为，这样做不便于维护，而且有大量重复代码，AOP的出现弥补了OOP的这点不足。为了更清楚阐述Spring AOP，我们从将以下方面进行讨论：
* 静态代理原理及实践。
* 动态代理原理及实践。
* Spring AOP原理及实战。
代理模式：为其他对象提供一种代理以控制对这个对象的访问。比如A对象要做一件事情，在没有代理前，自己来做，在对A代理后，由A的代理类B来做。代理其实是在原实例前后加了一层处理，这也是AOP的初级轮廓。
静态代理模式：静态代理说白了就是在程序运行前就已经存在代理类的字节码文件，代理类和原始类的关系在运行前就已经确定。

      package test.staticProxy;
      // 接口
      public interface IUserDao {
       void save();
       void find();
      }
      //目标对象
      class UserDao implements IUserDao{
       @Override
       public void save() {
        System.out.println("模拟：保存用户！");
       }
       @Override
       public void find() {
        System.out.println("模拟：查询用户");
       }
      }
      /**
        静态代理
        特点：
          1. 目标对象必须要实现接口
          2. 代理对象，要实现与目标对象一样的接口
       */
      class UserDaoProxy implements IUserDao{
       // 代理对象，需要维护一个目标对象
       private IUserDao target = new UserDao();
       @Override
       public void save() {
        System.out.println("代理操作： 开启事务...");
        target.save();   // 执行目标对象的方法
        System.out.println("代理操作：提交事务...");
       }
       @Override
       public void find() {
        target.find();
       }
      }

静态代理虽然保证了业务类只需关注逻辑本身，代理对象的一个接口只服务于一种类型的对象，如果要代理的方法很多，势必要为每一种方法都进行代理。还有就是如果增加一个方法，除了实现类需要实现这个方法外，所有的代理类也要实现此方法。增加了代码的维护成本。那如何解决呢？使用动态代理。动态代理模式：动态代理类的源码是在程序运行期间通过JVM反射等机制动态生成，代理类和委托类的关系是运行时才确定的。实例如下:

      import java.lang.reflect.InvocationHandler;
      import java.lang.reflect.Method;
      import java.lang.reflect.Proxy;
      // 接口
      public interface IUserDao {
       void save();
       void find();
      }
      //目标对象
       class UserDao implements IUserDao{
       @Override
       public void save() {
        System.out.println("模拟： 保存用户！");
       }
       @Override
       public void find() {
        System.out.println("查询");
       }
      }
        /**
         * 动态代理：
         * 代理工厂，给多个目标对象生成代理对象！
         *
         */
        class ProxyFactory {
         // 接收一个目标对象
         private Object target;
         public ProxyFactory(Object target) {
          this.target = target;
         }
         // 返回对目标对象(target)代理后的对象(proxy)
         public Object getProxyInstance() {
          Object proxy = Proxy.newProxyInstance(
           target.getClass().getClassLoader()，  // 目标对象使用的类加载器
           target.getClass().getInterfaces()，   // 目标对象实现的所有接口
           new InvocationHandler() {   // 执行代理对象方法时候触发
            @Override
            public Object invoke(Object proxy， Method method， Object[] args)
              throws Throwable {

             // 获取当前执行的方法的方法名
             String methodName = method.getName();
             // 方法返回值
             Object result = null;
             if ("find".equals(methodName)) {
              // 直接调用目标对象方法
              result = method.invoke(target， args);
             } else {
              System.out.println("开启事务...");
              // 执行目标对象方法
              result = method.invoke(target， args);
              System.out.println("提交事务...");
             }
             return result;
            }
           }
          );
          return proxy;
         }
        }

IUserDao proxy = (IUserDao)new ProxyFactory(target).getProxyInstance();其实是JDK动态生成了一个类去实现接口，隐藏了这个过程:class $jdkProxy implements IUserDao{}
使用jdk生成的动态代理的前提是目标类必须有实现的接口。如果某个类没有实现接口怎么办，就不能使用JDK动态代理，Cglib代理解决了这个问题。Cglib是以动态生成的子类继承目标的方式实现，在运行期动态的在内存中构建一个子类：public class $Cglib_Proxy_class  extends UserDao{}
`glib使用的前提是目标类不能为final修饰。因为final修饰的类不能被继承`

      AOP的定义：面向切面编程，核心原理是使用动态代理模式在方法执行前后或出现异常时加入相关逻辑。
      通过定义和前面代码我们可以发现3点：
      AOP是基于动态代理模式。
      AOP是方法级别的。
      AOP可以分离业务代码和关注点代码（重复代码），在执行业务代码时，动态的注入关注点代码。切面就是关注点代码形成的类。
spring AOP原理及实战:前文提到JDK代理和Cglib代理两种动态代理，优秀的Spring框架把两种方式在底层都集成了进去，我们无需担心自己去实现动态生成代理。那么，Spring是如何生成代理对象的？
* 创建容器对象的时候，根据切入点表达式拦截的类，生成代理对象。
* 如果目标对象有实现接口，使用jdk代理。如果目标对象没有实现接口，则使用Cglib代理。然后从容器获取代理后的对象，在运行期植入"切面"类的方法。如果目标类没有实现接口，且class为final修饰的，则不能进行Spring AOP编程！
知道了原理，现在我们将自己手动实现Spring的AOP：

      import org.aspectj.lang.ProceedingJoinPoint;
      public interface IUserDao {
       void save();
      }
      //用于测试Cglib动态代理
      class OrderDao {
       public void save() {
        //int i =1/0;用于测试异常通知
        System.out.println("保存订单...");
       }
      }
      //用于测试jdk动态代理
      class UserDao implements IUserDao {
       public void save() {
        //int i =1/0;用于测试异常通知
        System.out.println("保存用户...");
       }
      }
      //切面类
      class TransactionAop {
       public void beginTransaction() {
        System.out.println("[前置通知]  开启事务..");
       }
       public void commit() {
        System.out.println("[后置通知] 提交事务..");
       }
       public void afterReturing(){
        System.out.println("[返回后通知]");
       }
       public void afterThrowing(){
        System.out.println("[异常通知]");
       }
       public void arroud(ProceedingJoinPoint pjp) throws Throwable{
        System.out.println("[环绕前：]");
        pjp.proceed();          // 执行目标方法
        System.out.println("[环绕后：]");
       }
      }
        Spring的xml配置文件:
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:context="http://www.springframework.org/schema/context"
            xmlns:aop="http://www.springframework.org/schema/aop"
            xsi:schemaLocation="
                http://www.springframework.org/schema/beans
                http://www.springframework.org/schema/beans/spring-beans.xsd
                http://www.springframework.org/schema/context
                http://www.springframework.org/schema/context/spring-context.xsd
                http://www.springframework.org/schema/aop
                http://www.springframework.org/schema/aop/spring-aop.xsd">
         <!-- dao实例加入容器 -->
         <bean id="userDao" class="test.spring_aop_anno.UserDao"></bean>

         <!-- dao实例加入容器 -->
         <bean id="orderDao" class="test.spring_aop_anno.OrderDao"></bean>

         <!-- 实例化切面类 -->
         <bean id="transactionAop" class="test.spring_aop_anno.TransactionAop"></bean>

         <!-- Aop相关配置 -->
         <aop:config>
          <!-- 切入点表达式定义 -->
          <aop:pointcut expression="execution(* test.spring_aop_anno.*Dao.*(..))" id="transactionPointcut"/>
          <!-- 切面配置 -->
          <aop:aspect ref="transactionAop">
           <!-- 【环绕通知】 -->
           <aop:around method="arroud" pointcut-ref="transactionPointcut"/>
           <!-- 【前置通知】 在目标方法之前执行 -->
           <aop:before method="beginTransaction" pointcut-ref="transactionPointcut" />
           <!-- 【后置通知】 -->
           <aop:after method="commit" pointcut-ref="transactionPointcut"/>
           <!-- 【返回后通知】 -->
           <aop:after-returning method="afterReturing" pointcut-ref="transactionPointcut"/>
           <!-- 异常通知 -->
           <aop:after-throwing method="afterThrowing" pointcut-ref="transactionPointcut"/>
          </aop:aspect>
         </aop:config>
        </beans>
#### 示例

              导入5个jar(4个核心jar + commons-logging)：Spring的aop.jar、nspring-aop.jar、laspects.jar、nspring-aspects.jar
              <!-- 配置aop的代理 -->
              <!--注意:<aop:config proxy-target-class="true"> 如果这样配置则是强制使用CGLIB方式进行代理-->
              <aop:config>
              <!-- 定义一个切入点，起名为myPointCut，切入点是连接点的集合 -->
              <aop:pointcut expression="execution(public * com.briup.aop.service.*.*(..))" id="myPointCut"/>
              <!-- 定义哪一个advice在哪一个切入点上面起作用 -->
              <aop:advisor advice-ref="beforeAdvice" pointcut-ref="myPointCut" /></aop:config>
              expression="execution(public * com.briup.aop.service.*.*(..))"
              这个引号""里面就是用表达式的方式来定义切入点,只要是符合我们这个表达式要求的，方法就是我们的连接点,
              连接点的集合就是我们要定义的切入点。表达式中从左到右的*号:第一个* 表示方法的返回类型不限。第二个* 表示包中的任意一个类
              第三个* 表示类中的任意一个方法。同时方法的参数也没有限制.
#### 切面类
              public class XmlHandler {
                    public void beforeTest(JoinPoint p){
                        System.out.println(p.getSignature().getName()+" before...");
                    }
                    public void afterTest(JoinPoint p){
                        System.out.println(p.getSignature().getName()+" after...");
                    }

                    public void afterReturningTest(JoinPoint p){
                        System.out.println(p.getSignature().getName()+" afterReturning");
                    }
                    
                    //在和aroundAdvice结合的时候,这个方法一定要加上这个ProceedingJoinPoint类型的参数
                    public Object aroundTest(ProceedingJoinPoint pjp)throws Throwable{
                        //JoinPoint对象不能调用连接点所表示的方法 
                        //ProceedingJoinPoint能调用连接点所表示的方法 pjp.proceed()
                        System.out.println(pjp.getSignature().getName()+" is start..");
                        //调用到连接点方法
                        Object obj = pjp.proceed();
                        System.out.println(pjp.getSignature().getName()+" is end..");
                        return obj;
                    }

                  public void throwingTest(JoinPoint p,Exception ex){
                        System.out.println(p.getSignature().getName()+" is throwing..."+ex.getMessage());

                  }
                }
#### 代码示例
              <!-- 配置dao层对象 -->
              <bean id="dao" class="com.briup.aop.dao.AccountDaoImpl"/>
                  <!-- 配置目标对象 -->
                  <bean name="target" class="com.briup.aop.service.AccountServiceImpl">
                  <property name="accountDao" ref="dao"></property>
              </bean>
              <!-- 配置切面类 -->
              <bean name="handler" class="com.briup.aop.xml.XmlHandler"></bean>
              <!-- 配置aop的代理 -->
              <aop:config>
                <!-- 定义切入点名为myPointCut -->
                <aop:pointcut expression="execution(public * com.briup.aop.service.*.*(..))" id="myPointCut"/>
                  <!-- 定义切面类 以及需要使用的advice -->
                <aop:aspect id="aspect" ref="handler">
                  <!-- 表示beforeAdvice会把切面类handler中的beforeTest方法织入到名字叫myPointCut的切入点上面 -->
                  <aop:before method="beforeTest" pointcut-ref="myPointCut"/>
                  <!-- after表示不管方法是否正常结束都会起作用 -->
                  <aop:after method="afterTest" pointcut-ref="myPointCut"/>
                  <!-- after-returning表示方法正常结束才会起作用(抛异常时候不起作用) -->
                  <aop:after-returning method="afterReturningTest" pointcut-ref="myPointCut"/>
                  <aop:around method="aroundTest" pointcut-ref="myPointCut"/>
                  <!-- throwing="ex"表示throwingTest方法中接收异常对象的名字一定要是ex -->
                  <aop:after-throwing method="throwingTest" pointcut-ref="myPointCut" throwing="ex"/>
                </aop:aspect>
              </aop:config>
