#### 控制反转
###### 当一个对象创建时, 它所依赖的对象由外部传递给它, 而非自己去创建所依赖的对象(比如通过new操作). 因此,也可以说在对象如何获取他所依赖的对象这件事情上, 控制权反转了。
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
#### IOC底层原理
###### Xml解析
###### 工厂设计模式
###### 单例模式
###### 类的反射
