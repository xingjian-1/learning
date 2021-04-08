#### 面向切面
是spring框架中的一个热点和优势,利用AOP可以对业务的各个部分进行隔离,从而使得业务逻辑各个部分之间的耦合度降低.提高代码的复用性,同时提高开发效率，应用场景:日志, 事务管理, 缓存, 性能检测等。

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
