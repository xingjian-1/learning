#### 根据字符串得到类名
  
    String name = "听";
    Class c1 = name.getClass();
    System.out.println(c1.getName());
    
    String name = "java.lang.String";
     Class c1 = null;
     try {
            c1 = Class.forName(name);
            System.out.println(c1.getName());
        } catch (ClassNotFoundException e) {
    }
    输出：java.lang.String
#### 两个公有的含参构造方法和一个私有的含参构造方法以及一个公有的无参构造方法
    public class Test {

        private int age;
        private String name;
        private int testint;

        public Test(int age) {
            this.age = age;
        }

        public Test(int age, String name) {
            this.age = age;
            this.name = name;
        }

        private Test(String name) {
            this.name = name;
        }

        public Test() {
        }
###### 获取类的所有构造方法

     Test test = new Test();
     Class c4 = test.getClass();
     Constructor[] constructors ;
     constructors = c4.getDeclaredConstructors();
