#### 框架原理示例
* 创建一个Person类：

                    public class Person {
                        private String name;
                        private int age;
                        private Book book;
                        public String getName() {
                            return name;
                        }
                        public void setName(String name) {
                            this.name = name;
                        }
                        public int getAge() {
                            return age;
                        }
                        public void setAge(int age) {
                            this.age = age;
                        }
                        public Book getBook() {
                            return book;
                        }
                        public void setBook(Book book) {
                            this.book = book;
                        }
* 创建一个book类：

                    public class Book {
                        private String name;
                        private int price;
                        private String place;
                        public String getName() {
                            return name;
                        }
                        public void setName(String name) {
                            this.name = name;
                        }
                        public int getPrice() {
                            return price;
                        }
                        public void setPrice(int price) {
                            this.price = price;
                        }
                        public String getPlace() {
                            return place;
                        }
                        public void setPlace(String place) {
                            this.place = place;
                        }
                      }
* applicationContext.xml配置文件:

                    <?xml version="1.0" encoding="UTF-8"?>
                    <beans xmlns="http://www.springframework.org/schema/beans"
                        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
                    <!-- bean的配置文件 -->
                    <bean id="person" class="org.jingdong.bean.life.Person">
                    <property name="name" value="grl"></property>
                    <property name="age" value="11"></property> 
                    <property name="book" ref="book"></property></bean>
                    
                    <bean id="book" class="org.jingdong.bean.life.Book">
                    <property name="name" value="think in java"></property>
                    <property name="place" value="USA"></property>
                    <property name="price" value="79"></property>
                    </beans>
* main方法

                       public class Main {
                              public static void main(String[] args) {
                                  //创建IOC容器
                                  ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
                                  //从容器中获取bean实例
                                  Person person = (Person) ac.getBean("person");
                                  //使用bean
                                  System.out.println(person.getName());
                                   }
                                 }
                    
 * 运行原理解析：
 ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext .xml")被执行时，Spring容器对象被创建，同时applicationContext.xml中的bean会被加载到内存中。
 
                    BeanFactory和ApplicationContext区别
                    1.ApplicationContext：加载xml配置文件时，配置文件中的配置的bean已经被实例化<br>
                    2.BeanFactory：在加载配置文件时，配置文件中的bean不被实例化，只有当通过getBean(),获取bean实例的时候才被创建。<br>
                    总结：通过BeanFactory配置的bean比通过ApplicationContext配置的节约内存。
 * Spring Bean的生命周期
 
                    org.springframework.beans.factory.xml.XmlBeanDefinitionReader loadBeanDefinitions
                    信息: Loading XML bean definitions from class path resource [springBeanTest/beans.xml]
                    这是BeanFactoryPostProcessor实现类构造器！！
                    BeanFactoryPostProcessor调用postProcessBeanFactory方法
                    这是BeanPostProcessor实现类构造器！！
                    这是InstantiationAwareBeanPostProcessorAdapter实现类构造器！！
                     org.springframework.beans.factory.support.DefaultListableBeanFactory preInstantiateSingletons
                    信息: Pre-instantiating singletons in org.springframework.beans.factory.support.DefaultListableBeanFactory@9934d4: defining beans [beanPostProcessor,instantiationAwareBeanPostProcessor,beanFactoryPostProcessor,person]; root of factory hierarchy
                    InstantiationAwareBeanPostProcessor调用postProcessBeforeInstantiation方法
                    【构造器】调用Person的构造器实例化
                    InstantiationAwareBeanPostProcessor调用postProcessPropertyValues方法
                    【注入属性】注入属性address
                    【注入属性】注入属性name
                    【注入属性】注入属性phone
                    【BeanNameAware接口】调用BeanNameAware.setBeanName()
                    【BeanFactoryAware接口】调用BeanFactoryAware.setBeanFactory()
                    BeanPostProcessor接口方法postProcessBeforeInitialization对属性进行更改！
                    【InitializingBean接口】调用InitializingBean.afterPropertiesSet()
                    【init-method】调用<bean>的init-method属性指定的初始化方法
                    BeanPostProcessor接口方法postProcessAfterInitialization对属性进行更改！
                    InstantiationAwareBeanPostProcessor调用postProcessAfterInitialization方法
                    容器初始化成功
                    Person [address=广州, name=张三, phone=110]
                    现在开始关闭容器！
                    【DiposibleBean接口】调用DiposibleBean.destory()
                    【destroy-method】调用<bean>的destroy-method属性指定的初始化方法

###### 在BeanFactory里只对IOC容器的基本行为作了定义，根本不关心你的bean是如何定义怎样加载的。正如我们只关心工厂里得到什么的产品对象，至于工厂是怎么生产这些对象的，这个基本的接口不关心。而要知道工厂是如何产生对象的，我们需要看具体的IOC容器实现，spring提供了许多IOC容器的实现。比如XmlBeanFactory，ClasspathXmlApplicationContext等

               public interface BeanFactory {   
                    //对FactoryBean的转义定义，因为如果使用bean的名字检索FactoryBean得到的对象是工厂生成的对象，    
                    //如果需要得到工厂本身，需要转义           
                    String FACTORY_BEAN_PREFIX = "&"; 
                       
                    //根据bean的名字，获取在IOC容器中得到bean实例    
                    Object getBean(String name) throws BeansException;    
                  
                   //根据bean的名字和Class类型来得到bean实例，增加了类型安全验证机制。    
                    Object getBean(String name, Class requiredType) throws BeansException;    
                   
                   //提供对bean的检索，看看是否在IOC容器有这个名字的bean    
                    boolean containsBean(String name);    
                   
                   //根据bean名字得到bean实例，并同时判断这个bean是不是单例    
                   boolean isSingleton(String name) throws NoSuchBeanDefinitionException;    
                   
                   //得到bean实例的Class类型    
                   Class getType(String name) throws NoSuchBeanDefinitionException;    
                   
                   //得到bean的别名，如果根据别名检索，那么其原名也会被检索出来    
                  String[] getAliases(String name);                
               }
* 类的反射机制
* 接口继承接口，抽象类实现接口，实现类继承抽象类
