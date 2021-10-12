##### 什么是反射
反射就是在程序运行过程中，动态的从类的字节码文件(class文件)中获取类或者对象中的属性。
优缺点：1.可以在程序运行的过程中，动态的操作这些对象。2。可以解耦，提高程序的可扩展性。缺点：1.因为是JVM操作，所以对于性能来说会有所下降。2。容易对程序源码造成一定的混乱。
##### 反射的用法
三种反射方式获得实例对象：

          public class ReflectDemo {
              public static void main(String[] args) throws ClassNotFoundException {
              // Class.forName("");
              Class c1 = Class.forName("com.api.reflect.User");
              System.out.println(c1);
              // 类名.class
              Class<User> c2 = User.class;
              System.out.println(c2);
              // .getClass
              User user = new User();
              Class c3 = user.getClass();
              System.out.println(c3);

              System.out.println(c1 == c2);
              System.out.println(c1 == c3);
              System.out.println(c2 == c3);
            }
        }
获取成员变量:
        
        public Field getField(String name)：获取指定名称的成员变量(public)。
        public Field[] getFields()：获取全部成员变量(public)。
        public Field getDeclaredField(String name)：不考虑修饰符。
        public Field[] getDeclaredFields()：不考虑修饰符。
        
        public class User implements Serializable {
        public String name;
        protected Integer age;
        Integer sex;
        private String phone;
        public User() {
        }
        public User(String name, Integer age, Integer sex, String phone) {
            this.name = name;
            this.age = age;
            this.sex = sex;
            this.phone = phone;
        }
        private User(String name, Integer age){
            this.name = name;
            this.age = age;
        }
        .........省略set、get
        public void setPhone(String phone) {
            this.phone = phone;
        }
        @Override
        public String toString() {
            return "User{" +
                    "name='" + name + '\'' +
                    ", age=" + age +
                    ", sex=" + sex +
                    ", phone='" + phone + '\'' +
                    '}';
        }
         public void run() {
            System.out.println("跑步...");
        }

        private void eat(String username) {
            System.out.println(username + "正在吃饭...");
        }
        }
        上面的user对象编译成class文件，下面程序用反射机制获取成员变量;
        
        public static void main(String[] args) throws Exception {
        // 1，通过Class.forName方式获取User对象
        Class aClass = Class.forName("com.api.reflect.User");
        // 获取public修饰的成员变量
        Field[] fields = aClass.getFields();
        for (Field field : fields) {
            System.out.println("1，public修饰的成员变量--->" + field);
        }

        // 2，获取指定成员变量名称
        Field name = aClass.getField("name");
        System.out.println("2，指定成员变量名称--->" + name);

        // 3，获取全部的成员变量，忽略修饰符
        Field[] declaredFields = aClass.getDeclaredFields();
        for (Field declaredField : declaredFields) {
            System.out.println("3，不考虑修饰符，获取全部成员变量--->" + declaredField);
        }
        // 4，获取指定成员变量
        Field phone = aClass.getDeclaredField("phone");
        // 获取访问权限，暴力反射
        phone.setAccessible(true);
        System.out.println("4，暴力反射--->" + phone);
        }
构造方法:

        public Constructor<T> getConstructor(Class<?>... parameterTypes)
        public Constructor<?>[] getConstructors()
        public Constructor<T> getDeclaredConstructor(Class<?>... parameterTypes)
        public Constructor<?>[] getDeclaredConstructors()
        
        public static void main(String[] args) throws Exception {
        // 通过Class.forName方式获取User对象
        Class aClass = Class.forName("com.api.reflect.User");
        // 1，获取指定构造方法(public)，参数是可变参数
        Constructor constructor = aClass.getConstructor(String.class, Integer.class, Integer.class, String.class);
        // 实例化对象
        Object user = constructor.newInstance("张三", 20, 1, "123456");
        System.out.println(user);

        // 2，获取全部构造方法(public)
        Constructor[] constructors = aClass.getConstructors();
        for (Constructor constructor1 : constructors) {
            System.out.println(constructor1);
        }

        // 3，不考虑修饰符，获取指定构造方法
        Constructor declaredConstructor = aClass.getDeclaredConstructor(String.class, Integer.class);
        // 获取权限
        declaredConstructor.setAccessible(true);
        Object declaredUser = declaredConstructor.newInstance("李四", 21);
        System.out.println("不考虑修饰符--->" + declaredUser);

        // 4，获取全部构造方法
        Constructor[] declaredConstructors = aClass.getDeclaredConstructors();
        for (Constructor declaredConstructor1 : declaredConstructors) {
            System.out.println("不考虑修饰符--->" + declaredConstructor1);
          }
        }
成员方法：

        public Method getMethod(String name,Class<?>... parameterTypes)
        public Method[] getMethods()
        public Method getDeclaredMethod(String name,Class<?>... parameterTypes）
        public Method[] getDeclaredMethods()
        
        public static void main(String[] args) throws Exception {
        // 通过Class.forName方式获取User对象
        Class aClass = Class.forName("com.api.reflect.User");
        // 1，获取指定成员方法，public修饰
        Method methodRun = aClass.getMethod("run");
        // 实例化User，并调用invoke()执行方法
        Object user = aClass.newInstance();
        methodRun.invoke(user);
        // 2，获取全部成员方法，public修饰
        Method[] methods = aClass.getMethods();
        for (Method method : methods) {
            System.out.println(method);
        }
        // 3，获取私有成员方法
        Method eat = aClass.getDeclaredMethod("eat", String.class);
        // 获取权限
        eat.setAccessible(true);
        // 执行方法
        eat.invoke(user,"小李");
        }
更多介绍看Oracle官网Java各个版本的API文档解释：https://www.oracle.com/cn/java/technologies/java-se-api-doc.html   
##### 总结
关于反射中的常用方法就总结到此，反射的用处还是很多的，比如Spring中IOC，DI都是利用反射机制。
