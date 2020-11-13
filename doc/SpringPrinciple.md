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
 ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext .xml")被执行时，Spring容器对象被创建，同时applicationContext.xml中的bean会被加载到内存中
 
                    BeanFactory和ApplicationContext区别
                    1.ApplicationContext：加载xml配置文件时，配置文件中的配置的bean已经被实例化<br>
                    2.BeanFactory：在加载配置文件时，配置文件中的bean不被实例化，只有当通过getBean(),获取bean实例的时候才被创建。<br>
                    总结：通过BeanFactory配置的bean比通过ApplicationContext配置的节约内存。
