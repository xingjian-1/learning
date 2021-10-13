##### 泛型
Java泛型（generics）是JDK5中引入的一个新特性，指所操作的数据类型被指定为一个参数。在没有泛型的情况的下，通过对类型Object的引用来实现参数的“任意化”，“任意化”带来的缺点是要做显式的强制类型转换，而这种转换是要求开发者对实际参数类型可以预知的情况下进行的。对于强制类型转换错误的情况，编译器可能不提示错误，在运行的时候才出现异常，这是本身就是一个安全隐患。那么泛型的好处就是在编译的时候能够检查类型安全，并且所有的强制转换都是自动和隐式的。

            ？ 表示不确定的java类型
            T (type) 表示具体的一个java类型
            K V (key value) 分别代表java键值中的Key Value
            E (element) 代表Element

##### ？无界通配符

            有一个父类Animal和N个子类：
            static int countLegs (List<? extends Animal > animals ) {
                int retVal = 0;
                for (Animal animal : animals){
                    retVal += animal.countLegs();
                }
                return retVal;
            }

            static int countLegs1 (List<Animal> animals){
                int retVal = 0;
                for (Animal animal : animals ){
                    retVal += animal.countLegs();
                }
                return retVal;
            }

            public static void main(String[] args) {
                List<Dog> dogs = new ArrayList<>();
              // 不会报错
                countLegs( dogs );
             // 报错
                countLegs1(dogs);
            }
对于不确定要操作的类型，可以使用无限制通配符（即 <?>），表示可以持有任何类型。像countLegs方法中，限定了上界，但是不关心具体类型是什么，所以对于传入的Animal的所有子类都可以支持。而countLegs1 就不行。

##### 上界通配符 < ? extends E>
上届：用extends关键字声明，表示参数化的类型可能是所指定的类型，或者是此类型的子类。在类型参数中使用extends表示这个泛型中的参数必须是E或者E的子类，这样有两个好处：

            如果传入的类型不是E 或者 E 的子类，编译不成功        
            private <K extends A, E extends B> E test(K arg1, E arg2){
                E result = arg2;
                arg2.compareTo(arg1);
                //.....
                return result;
            }
            类型参数列表中如果有多个类型参数上限，用逗号分开

##### 下界通配符 < ? super E>
下界: 用super进行声明，表示参数化的类型可能是所指定的类型，或者是此类型的父类型

            private <T> void test(List<? super T> dst, List<T> src){
                for (T t : src) {
                    dst.add(t);
                }
            }
            public static void main(String[] args) {
                List<Dog> dogs = new ArrayList<>();
                List<Animal> animals = new ArrayList<>();
                new Test3().test(animals,dogs);
            }
            // Dog 是 Animal 的子类
            class Dog extends Animal {

            }
dst类型 “大于等于” src 的类型，这里的“大于等于”是指 dst 表示的范围比 src 要大，因此装得下 dst 的容器也就能装 src 。

##### ？和 T 的区别
T 是一个确定的类型，通常用于泛型类和泛型方法的定义，？是一个 不确定 的类型，通常用于泛型方法的调用代码和形参，不能用于定义类和泛型方法。比如如下这种 ：
* 区别1：通过T来确保泛型参数的一致性

                public <T extends Number> void test(List<T> dest, List<T> src)
                //通配符是不确定的，所以这个方法不能保证两个List具有相同的元素类型
                public void test(List<? extends Number> dest, List<? extends Number> src)

* 区别2：类型参数可以多重限定而通配符不行

              public class Multilimit implements MultilimitInterfaceA,MultilimitInterfaceB{
                        //使用&符号设定多重边界
                        public static<T extends MultilimitInterfaceA & MultilimitInterfaceB> void test(T t){
                                    }
               }

             interface MultilimitInterfaceA{}
             interface MultilimitInterfaceB{}

* 区别3：通配符可以使用超类限定而类型参数不行
类型参数 T只具有一种类型限定方式：

                T extends A
但是通配符 ? 可以进行 两种限定：

                ? extends A
                ? super A
#### Class<T>和 Class<?>区别
常见的是在反射场景下的使用

                // 通过反射的方式生成multiLimit对象
                MultiLimit multiLimit = (MultiLimit)
                Class.forName("com.glmapper.bridge.boot.generic.MultiLimit").newInstance();
                对于上述代码，在运行期，如果反射的类型不是MultiLimit类，那么一定会报 ava.lang.ClassCastException错误。

                Class<T>在实例化的时候，T 要替换成具体类。Class<?>它是个通配泛型，? 可以代表任何类型，所以主要用于声明时的限制情况。
