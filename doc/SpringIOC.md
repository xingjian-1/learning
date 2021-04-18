#### 控制反转
IOC(Inversion of Control)，即控制反转，意思是将对象的创建和依赖关系交给第三方容器处理，用的时候告诉容器需要什么然后直接去拿就行了。举个例子，我们有一个工厂，它生产各种产品，当你需要某个产品，比如你需要一辆汽车，你就告诉工厂你需要一辆汽车，工厂就会直接返回给你一辆汽车，而不需要你自己通过付出劳动来得到这辆汽车，你也不用关心工厂是如何生产这辆汽车。程序中，IOC容器会帮我们创建和管理对象，当你告诉容器你需要某个对象时，容器会把这个对象返回给你，而不需要自己去new出一个对象来，对象的创建和管理会由容器自动进行，直接从容器中拿来用就可以了。IOC可以说是Spring最核心的思想，它使我们的开发变得简单（对象之间的依赖关系可以通过配置文件或者注解来建立），对于这种优秀的设计思想，让我们研究一下它的底层实现原理吧！

         package org.springframework.beans.factory;
         import org.springframework.beans.BeansException;
         import org.springframework.core.ResolvableType;
         import org.springframework.lang.Nullable;
         public interface BeanFactory {
             String FACTORY_BEAN_PREFIX = "&";
             Object getBean(String name) throws BeansException;
             <T> T getBean(String name, @Nullable Class<T> requiredType) throws BeansException;
             Object getBean(String name, Object... args) throws BeansException;
             <T> T getBean(Class<T> requiredType) throws BeansException;
             <T> T getBean(Class<T> requiredType, Object... args) throws BeansException;
             boolean containsBean(String name);
             boolean isSingleton(String name) throws NoSuchBeanDefinitionException;
             boolean isPrototype(String name) throws NoSuchBeanDefinitionException;
             boolean isTypeMatch(String name, ResolvableType typeToMatch) throws NoSuchBeanDefinitionException;
             boolean isTypeMatch(String name, @Nullable Class<?> typeToMatch) throws NoSuchBeanDefinitionException;
             @Nullable
             Class<?> getType(String name) throws NoSuchBeanDefinitionException;
             String[] getAliases(String name);
         }   
这个接口便是spring核心的bean工厂定义，它是IOC容器的顶层接口，spring中所有bean工厂都直接或间接的继承或实现了这个接口。我们平时使用的最多的ApplicationContext接口也继承了BeanFactory接口，因此它具有BeanFactory接口的所有功能，这里顺便提一下，从BeanFactory获取bean时，实例化BeanFactory容器并不会实例化所配置的bean，只有当使用某个bean(getBean)时，才会实时的实例化该bean；ApplicationContext获取bean时，实例化ApplicationContext容器时会一并实例化容器中的所有的bean。从BeanFactory的源码可以看出，它实现的核心功能就是根据名称或类型来返回一个bean实例。一个工厂如果要具备这种功能，结合工厂模式的思想，我们可以试想一下它需要具备以下几个条件：

* 1、持有各种bean的定义，只有拿到了bean的定义信息，才能根据这些信息进行实例化；
* 2、持有各种bean之间的依赖关系，如果一个类中持有对另一个类的引用，那么在对该类进行实例化时，必须根据类之间的依赖关系对相关类也进行实例化，因此，工厂必须获得类之间的依赖关系，否则无法正确实例化；
* 3、以上两种信息都依赖于我们的配置信息定义，比如xml配置文件，工厂需要一个工具来读取配置文件的信息。

以上是我们设想IOC的实现思路，只要满足以上三个条件，就能构造一个工厂，生产各种bean。但是我们还是有一些疑问，比如在第一个条件中，我们如何持有bean的定义呢？我们先来看另外一个接口：

         package org.springframework.beans.factory.config;

         import org.springframework.beans.BeanMetadataElement;
         import org.springframework.beans.MutablePropertyValues;
         import org.springframework.core.AttributeAccessor;
         import org.springframework.lang.Nullable;

         public interface BeanDefinition extends AttributeAccessor, BeanMetadataElement {
             String SCOPE_SINGLETON = ConfigurableBeanFactory.SCOPE_SINGLETON;
             String SCOPE_PROTOTYPE = ConfigurableBeanFactory.SCOPE_PROTOTYPE;
             int ROLE_APPLICATION = 0;
             int ROLE_SUPPORT = 1;
             int ROLE_INFRASTRUCTURE = 2;
             void setParentName(@Nullable String parentName);
             @Nullable
             String getParentName();
             void setBeanClassName(@Nullable String beanClassName);
             @Nullable
             String getBeanClassName();
             void setScope(@Nullable String scope);
             @Nullable
             String getScope();
             void setLazyInit(boolean lazyInit);
             boolean isLazyInit();
             /**
              * Set the names of the beans that this bean depends on being initialized.
              * The bean factory will guarantee that these beans get initialized first.
              */
             void setDependsOn(String... dependsOn);
             /**
              * Return the bean names that this bean depends on.
              */
             @Nullable
             String[] getDependsOn();
             void setAutowireCandidate(boolean autowireCandidate);
             boolean isAutowireCandidate();
             void setPrimary(boolean primary);
             boolean isPrimary();
             void setFactoryBeanName(@Nullable String factoryBeanName);
             @Nullable
             String getFactoryBeanName();
             void setFactoryMethodName(@Nullable String factoryMethodName);
             @Nullable
             String getFactoryMethodName();
             ConstructorArgumentValues getConstructorArgumentValues();
             MutablePropertyValues getPropertyValues();
             boolean isSingleton();
             boolean isPrototype();
             boolean isAbstract();
             int getRole();
             @Nullable
             String getDescription();
             @Nullable
             String getResourceDescription();
             @Nullable
             BeanDefinition getOriginatingBeanDefinition();
         }
BeanDefinition，便是spring中的bean定义接口，spring的工厂里持有的就是此接口定义的内容。从源码可以看出，这个接口继承了另外两个接口，一个是AttributeAccessor接口，继承这个接口就意味着BeanDefinition接口拥有了处理属性的能力，另外一个接口是BeanMetedataElement，它可以获得bean的配置定义的元素，对于xml文件来说就是会持有bean的标签。
从源码中我们可以看出，BeanDefinition接口定义了两个方法，分别是void setDependsOn(String... dependsOn)和String[] getDependsOn()，这两个方法就是设置依赖的bean的名称和获取依赖的bean的名称，这就意味着只要我们有一个BeanDefinition，就能得到bean的定义信息和bean之间的依赖关系，从而可以生产一个完整的bean实例。
上面两个接口，我们大致可以猜出spring是如何持有bean的定义信息及依赖关系了，没错，就是让bean工厂持有一个Map<String，BeanDefinition>，String型的beanName作为key，BeanDefinition型的bean定义作为value，这样就能生产一个bean实例。BeanFactory接口当然不能持有这个map对象，那么一定是在它的某个实现类里所持有的，我们找到了这个实现类，来看看源码：

         @SuppressWarnings("serial")
         public class DefaultListableBeanFactory extends AbstractAutowireCapableBeanFactory
                 implements ConfigurableListableBeanFactory, BeanDefinitionRegistry, Serializable {
             @Nullable
             private static Class<?> javaxInjectProviderClass;
             static {
                 try {
                     javaxInjectProviderClass =
                             ClassUtils.forName("javax.inject.Provider", DefaultListableBeanFactory.class.getClassLoader());
                 }
                 catch (ClassNotFoundException ex) {
                     // JSR-330 API not available - Provider interface simply not supported then.
                     javaxInjectProviderClass = null;
                 }
             }
             /** Map from serialized id to factory instance */
             private static final Map<String, Reference<DefaultListableBeanFactory>> serializableFactories =
                     new ConcurrentHashMap<>(8);

             /** Optional id for this factory, for serialization purposes */
             @Nullable
             private String serializationId;

             /** Whether to allow re-registration of a different definition with the same name */
             private boolean allowBeanDefinitionOverriding = true;

             /** Whether to allow eager class loading even for lazy-init beans */
             private boolean allowEagerClassLoading = true;

             /** Optional OrderComparator for dependency Lists and arrays */
             @Nullable
             private Comparator<Object> dependencyComparator;

             /** Resolver to use for checking if a bean definition is an autowire candidate */
             private AutowireCandidateResolver autowireCandidateResolver = new SimpleAutowireCandidateResolver();

             /** Map from dependency type to corresponding autowired value */
             private final Map<Class<?>, Object> resolvableDependencies = new ConcurrentHashMap<>(16);

             /** Map of bean definition objects, keyed by bean name */
             //beanFactory持有此map,这样就可以在任何时候获取bean的BeanDefinition来创建一个bean实例
             private final Map<String, BeanDefinition> beanDefinitionMap = new ConcurrentHashMap<>(256);

             /** Map of singleton and non-singleton bean names, keyed by dependency type */
             private final Map<Class<?>, String[]> allBeanNamesByType = new ConcurrentHashMap<>(64);

             /** Map of singleton-only bean names, keyed by dependency type */
             private final Map<Class<?>, String[]> singletonBeanNamesByType = new ConcurrentHashMap<>(64);

             /** List of bean definition names, in registration order */
             private volatile List<String> beanDefinitionNames = new ArrayList<>(256);

             /** List of names of manually registered singletons, in registration order */
             private volatile Set<String> manualSingletonNames = new LinkedHashSet<>(16);

             /** Cached array of bean definition names in case of frozen configuration */
             @Nullable
             private volatile String[] frozenBeanDefinitionNames;

             /** Whether bean definition metadata may be cached for all beans */
             private volatile boolean configurationFrozen = false;
         }
         /** Map of bean definition objects, keyed by bean name */
         //beanFactory持有此map,这样就可以在任何时候获取bean的BeanDefinition来创建一个bean实例
         private final Map<String, BeanDefinition> beanDefinitionMap = new ConcurrentHashMap<>(256);
从方法是说明就可以看出这是bean定义的map对象，以bean的名称作为key。到这里思路就明确了，bean工厂的初始化就是往这个map对象里加东西，把我们xml文件里定义的bean填充到这个对象里，bean工厂就可以工作了
##### 示例
* 写一个Person类作为bean：

         public class Person {

             public void work(){
                 System.out.println("I am working...");
             }
         }
* 创建一个applicationContext.xml配置文件，配置bean：

         <?xml version="1.0" encoding="UTF-8"?>
         <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://www.springframework.org/schema/beans 
             http://www.springframework.org/schema/beans/spring-beans.xsd">

             <bean id="person" class="com.springframework.bean.test.Person"></bean>

         </beans>
* 测试类：
         
         public class Client {
             public static void main(String[] args) {
                 ClassPathResource classPathResource = new ClassPathResource("applicationContext.xml");
                 DefaultListableBeanFactory defaultListableBeanFactory = new DefaultListableBeanFactory();
                 XmlBeanDefinitionReader beanDefinitionReader = new XmlBeanDefinitionReader(defaultListableBeanFactory);
                 beanDefinitionReader.loadBeanDefinitions(classPathResource);
                 System.out.println(defaultListableBeanFactory.getBeanDefinitionCount());
                 Person person = (Person)defaultListableBeanFactory.getBean("person");
                 person.work();
             }
         }
* 执行结果如下：
         
         七月 06, 2020 9:41:48 下午 org.springframework.beans.factory.xml.XmlBeanDefinitionReader loadBeanDefinitions
         信息: Loading XML bean definitions from class path resource [applicationContext.xml]
         1
         I am working...

         从结果可以看出，我们成功解析了xml文件，并注册了一个bean定义，通过getBean()方法成功返回了一个实例。上面的测试类用4行代码实现了bean工厂的初始化：
         第一行，完成了资源定位；
         第二行，创建了一个默认的bean工厂；
         第三行，创建了一个Reader，这个Reader用来读取xml文件，将创建的defaultListableBeanFactory 作为参数传递给Reader，表示为此工厂创建Reader；
         第四行，用Reader读取配置信息，并将解析的bean定义注册到defaultListableBeanFactory 中。
         执行完以上四个步骤，bean工厂酒杯正确初始化了，接下来我们可以调用工厂的方法，以及获得bean实例。

         但是在实际开发中不会这么复杂，spring可以更简单的一步到位，它是这么做的
         public class TestSpringBeanFactory {
             public static void main(String[] args) {
                 ApplicationContext ctx = new FileSystemXmlApplicationContext("src/applicationContext.xml");
                 System.out.println(ctx.getBeanDefinitionCount());
                 Person person = (Person) ctx.getBean("person");
                 person.work();
             }
         }
         我们看看执行结果：
         七月 06, 2017 9:42:55 下午 org.springframework.context.support.AbstractApplicationContext prepareRefresh
         信息: Refreshing org.springframework.context.support.FileSystemXmlApplicationContext@20ad9418:
         startup date [Thu Jul 06 21:42:55 CST 2017]; root of context hierarchy
         七月 06, 2017 9:42:55 下午 org.springframework.beans.factory.xml.XmlBeanDefinitionReader loadBeanDefinitions
         信息: Loading XML bean definitions from file [C:\Users\fangfuhai\workspace\spring-code-learning\src\applicationContext.xml]
         1
         I am working...
         从结果可以看出，spring用一行代码就完成了我们四个步骤，仔细看看日志信息就可以发现，spring也是用XmlBeanDefinitionReader 
         来读取、解析并注册，同时在日志信息里还多了两行，这说明在这一行代码里，spring还做了更多的事情。。。。。。。。。。。。
#### 三种方式实现控制反转
###### XML配置bean

         1.需要创建一个工厂类,并提供一个静态方法返回目标类的对象
          Xml文件配置bean的时候class属性需要指向工厂类的路径,同时需要指定factory-method属性,属性值为工厂的静态方法：
          <?xml version="1.0" encoding="UTF-8"?>
          <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="
          http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd">
          
          <bean id="userAction" class="com.cdsxt.action.UserAction"></bean>
          <!-- 静态工厂创建bean -->
          
          <beanid="userAction1" class="com.cdsxt.factory.UserActionFactory" factory-method="getUserAction"></bean>
          </beans>
          
          
          
          2.创建实例工厂类,提供一个非静态的方法返回目标类的对象

          配置实例工厂bean
          配置目标类bean, 配置factory-bean属性,值指向工厂bean的id;配置factory-method属性,值为工厂类中的get方法
          <?xml version="1.0" encoding="UTF-8"?>
          <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="
          http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd">
          
          <bean id="userAction" class="com.cdsxt.action.UserAction"></bean>

          <!-- 实例工厂创建bean -->
          <bean id="userActionFactory" class="com.cdsxt.factory.UserActionFactory"></bean>
          <bean id="userAction1" factory-bean="userActionFactory" factory-method="getUserAction"></bean>
          </beans>
###### 依赖注入/set方法注入
              1.xml方式配置DI(基本类型):
              <?xml version="1.0" encoding="UTF-8"?>
              <beans xmlns="http://www.springframework.org/schema/beans"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="
              http://www.springframework.org/schema/beans
              http://www.springframework.org/schema/beans/spring-beans.xsd">
              <!-- 属性注入(基本类型) -->
              <bean id="phone" class="com.cdsxt.po.Phone">
              <property name="name" value="xiaomi"></property>
              <property name="price" value="2888.00"></property>
              </bean>
              </beans>
              
              2.set方法注入：
              import java.util.Map;
              import java.util.Properties;
              public class Dependency {
              private int[] array;
              private List<String> list;
              private Map<String, String> map;
              private Properties properties;
              public int[] getArray() {
              return array;
              }
              public void setArray(int[] array) {
              this.array = array;
              }
              public List<String> getList() {
              return list;
              }
              public void setList(List<String> list) {
              this.list = list;
              }
              public Map<String, String> getMap() {
              return map;
              }
              public void setMap(Map<String, String> map) {
              this.map = map;
              }
              public Properties getProperties() {
              return properties;
              }
              public void setProperties(Properties properties) {
              this.properties = properties;
              }
              }

              Xml配置文件:
              <?xml version="1.0" encoding="UTF-8"?>
              <beans xmlns="http://www.springframework.org/schema/beans"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="
              http://www.springframework.org/schema/beans
              http://www.springframework.org/schema/beans/spring-beans.xsd">
              <bean id="dependency" class="com.cdsxt.po.Dependency">
              <!-- 注入数组 -->
              <property name="array">
              <array>
              <value>1</value>
              <value>2</value>
              <value>3</value>
              </array>
              </property>
              <!-- 注入list -->
              <property name="list">
              <list>
              <value>list1</value>
              <value>list2</value>
              <value>list3</value>
              </list>
              </property>
              <!-- 注入map -->
              <property name="map">
              <map>
              <entry key="k1" value="value1"></entry>
              <entry key="k2" value="value2"></entry>
              <entry key="k3" value="value3"></entry>
              </map>
              </property>
              <!-- 注入properties -->
              <property name="properties">
              <props>
              <prop key="p1">v1</prop>
              <prop key="p2">v2</prop>
              <prop key="p3">v3</prop>
              </props>
              </property>
              </bean>
              </beans>
###### 注解方式注入：
                  导入jar，4+1(4个核心jar + commons-logging)，spring-aop : 使用注解会使用到该jar 
                  在applicationContext.xml中添加注解的配置：
                  <context:component-scan base-package=”要扫描的包”>
                  如果bean的装配使用xml,依赖的装配使用注解,可以使用以下配置
                  <context:annotation-config>
                
                在java类上添加@Component(“id”)注解(id可选),告诉spring这是一个组件,交由spring管理,这一步相当于xml当中的<bean>配置
                l除了@Component以外还有其他几个注解配置组件:
                @Controller
                @Service
                @Repository
                配置组件之后,如果未指定id,默认给对象配置一个类名首字母小写的id,如果以显式的配置了id,则默认的就不存在了
                如果有依赖,在依赖上添加@Autowired注解,使用该注解,没有set方法也可以
                除了@Autowired,@Resource也可以用来装配bean
                @Autowired默认按类型进行装配(该注解由spring提供),默认情况下必须要求依赖对象存在,如果要允许null值,可以设置required属性为false,如:@Autowired(required=false);如果想要使用名称装配,可以结合使用@Qualifier,如:@Autowired@Qualifier(“name”);
                @Resource(该注解由j2EE提供),默认按照名称进行装配,名称可以通过name属性进行指定,如果未指定name属性,当注解写在字段上是,默认取字段名查找,当找不到名称匹配的bean时,才按照类型进行装配.但是如果name属性一旦指定,将只会按照名称进行装配
###### Xml解析
###### 工厂设计模式
###### 单例模式
###### 类的反射
