#### Java容器分为Collection和Map两大类
##### Collection 
 `Collection`是一个集合接口，它提供了对集合对象进行基本操作的通用接口方法，所有集合都是它的子类，比如 List、Set 等。`Collections`是一个包装类，包含了很多静态方法，不能被实例化，就像一个工具类，比如提供的排序方法： Collections. sort(list)。
* List： ArrayList LinkedList Vector Stack
* Set： HashSet LinkedHashSet TreeSet
* Map： Hashtable HashMap ConcurrentHashMap LinkedHashMap TreeMap  
List、Set、Map 的区别主要体现在两个方面：元素是否有序、是否允许元素重复。

##### HashMap和Hashtable的区别 
存储：HashMap允许key和value为null，而Hashtable不允许。线程安全：Hashtable是线程安全的，而HashMap是非线程安全的。推荐使用：在 Hashtable 的类注释可以看到，Hashtable是保留类不建议使用，推荐在单线程环境下使用HashMap替代，如果需要多线程使用则用ConcurrentHashMap替代。

##### 如何决定使用HashMap还是TreeMap
对于在 Map 中插入、删除、定位一个元素这类操作，HashMap 是最好的选择，因为相对而言HashMap的插入会更快，但如果你要对一个key集合进行有序的遍历，那TreeMap是更好的选择。

##### HashMap的实现原理 
jdk1.8中HashMap存储结构是：数组+链表+红黑树，HashMap基于Hash算法实现的，我们通过put(key,value)存储，get(key)来获取。当传入 key时，HashMap会根据key.hashCode()计算出hash值，根据hash值定位到具体的数组存储位置，如果相同的位置已经存在相同的hash值，称为hash冲突，内容是否相同，不相同放到链表的尾部，当链表长度大于8时会转换为红黑树。[HashMap底层实现和原理](./doc/HashMap底层实现和原理.md)

##### HashSet的实现原理
HashSet是基于HashMap实现的，HashSet底层使用HashMap来保存所有元素，实现比较简单，相关HashSet的操作，基本上都是直接调用底层HashMap的相关方法来完成，HashSet不允许重复的值。

##### ArrayList和LinkedList的区别
* 查询：ArrayList 是动态数组的数据结构实现，而LinkedList是双向链表的数据结构实现。随机访问效率：ArrayList比LinkedList在随机访问的时候效率要高，因为LinkedList是线性的数据存储方式，所以需要移动指针从前往后依次查找。
* 增加和删除效率：在非首尾的增加和删除操作，LinkedList要比ArrayList效率要高，因为ArrayList增删操作要影响数组内的其他数据的下标。综合来说，在需要频繁读取集合中的元素时，更推荐使用ArrayList，而在插入和删除操作较多时，更推荐使用LinkedList。

##### 如何实现数组和List之间的转换
* 数组转List：使用Arrays. asList(array)进行转换。
* List转数组：使用List自带的toArray()方法。

                     List<String> list = new ArrayList<String>();
                             //list to array
                             list. add("小傻瓜");
                             list. add("的世界");
                             list. toArray();
                             //array to list
                             String[] array = new String[]{"小傻瓜","的世界"};
                             Arrays. asList(array);
##### ArrayList和Vector的区别
* 线程安全：Vector使用了Synchronized来实现线程同步，是线程安全的，而ArrayList是非线程安全的。
* 性能：ArrayList在性能方面要优于 Vector。
* 扩容：ArrayList和Vector都会根据实际的需要动态的调整容量，只不过在Vector扩容每次会增加 1 倍，而ArrayList只会增加50%。
##### Array和ArrayList区别
* Array可以存储基本数据类型和对象，ArrayList只能存储对象。
* Array是指定固定大小的，而ArrayList大小是自动扩展的。Array 内置方法没有 ArrayList 多，比如 addAll、removeAll、iteration 等方法只有 ArrayList 有。
##### 在Queue中poll()和remove()区别
* 相同点：都是返回第一个元素，并在队列中删除返回的对象。
* 不同点：如果没有元素 poll()会返回 null，而 remove()会直接抛出 NoSuchElementException 异常。
           
                    Queue<String> queue = new LinkedList<String>();
                            queue. offer("string"); // add
                            System. out. println(queue. poll());
                            System. out. println(queue. remove());
                            System. out. println(queue. size());
##### 哪些集合类是线程安全的
Vector、Hashtable、Stack 都是线程安全的，HashMap是非线程安全的，不过在 JDK 1.5 之后随着 Java.util.concurrent并发包的出现，它们也有了自己对应的线程安全类，比如HashMap对应的线程安全类就是 ConcurrentHashMap。

##### 迭代器Iterator
Iterator 接口提供遍历任何Collection的接口。我们可以从一个Collection中使用迭代器方法来获取迭代器实例。迭代器取代了Java集合框架中的Enumeration，迭代器允许调用者在迭代过程中移除元素。

##### Iterator怎么使用？有什么特点？
        
                    List<String> list = new ArrayList<>();
                            Iterator<String> it = list. iterator();
                            while(it. hasNext()){
                              String obj = it. next();
                              System. out. println(obj);
                            }
                    Iterator 的特点是更加安全，因为它可以确保，在当前遍历的集合元素被更改的时候，
                    就会抛出ConcurrentModificationException异常。

##### Iterator和ListIterator区别
* Iterator可以遍历Set和List集合，而ListIterator只能遍历List。
* Iterator只能单向遍历，而ListIterator可以双向遍历（向前/后遍历）。
* ListIterator 从 Iterator 接口继承，然后添加了一些额外的功能，比如添加一个元素、替换一个元素、获取前面或后面元素的索引位置。
##### 怎么确保一个集合不能被修改？

                   可以使用 Collections. unmodifiableCollection(Collection c) 方法来创建一个只读集合，这样改变集合的任何操作都会抛出
                   Java. lang. UnsupportedOperationException 异常。
                   示例代码如下：
                    List<String> list = new ArrayList<>();
                            list. add("x");
                            Collection<String> clist = Collections. unmodifiableCollection(list);
                            clist. add("y"); // 运行时此行报错
                            System. out. println(list. size());
##### final在Java中有什么作用
final修饰的类叫最终类，该类不能被继承。final 修饰的方法不能被重写。final 修饰的变量叫常量，常量必须初始化，初始化之后值就不能被修改。

##### Java中的 Math. round(-1. 5) 等于多少？
等于 -1，因为在数轴上取值时，中间值（0.5）向右取整，所以正 0.5 是往上取整，负 0.5 是直接舍弃。

##### String属于基础的数据类型吗？
String 不属于基础类型，基础类型有 8 种：byte、boolean、char、short、int、float、long、double，而 String 属于对象。

##### Java中操作字符串都有哪些类？它们之间有什么区别？
操作字符串的类有：String、StringBuffer、StringBuilder。
String 和 StringBuffer、StringBuilder 的区别在于 String 声明的是不可变的对象，每次操作都会生成新的 String 对象，然后将指针指向新的 String 对象，
而 StringBuffer、StringBuilder 可以在原有对象的基础上进行操作，所以在经常改变字符串内容的情况下最好不要使用 String。
StringBuffer 和 StringBuilder 最大的区别在于，StringBuffer 是线程安全的，而 StringBuilder 是非线程安全的，但 StringBuilder 的性能却高于 
StringBuffer，所以在单线程环境下推荐使用 StringBuilder，多线程环境下推荐使用 StringBuffer。

##### String str="i"与 String str=new String("i")一样吗？
不一样，因为内存的分配方式不一样。String str="i"的方式，Java 虚拟机会将其分配到常量池中；而 String str=new String("i") 则会被分到堆内存中。

##### 如何将字符串反转？
使用 StringBuilder 或者 stringBuffer 的 reverse() 方法。

                // StringBuffer reverse
                StringBuffer stringBuffer = new StringBuffer();
                stringBuffer. append("abcdefg");
                System. out. println(stringBuffer. reverse()); // gfedcba
                // StringBuilder reverse
                StringBuilder stringBuilder = new StringBuilder();
                stringBuilder. append("abcdefg");
                System. out. println(stringBuilder. reverse()); // gfedcba

##### String 类的常用方法都有那些？

                indexOf()：返回指定字符的索引。
                charAt()：返回指定索引处的字符。
                replace()：字符串替换。
                trim()：去除字符串两端空白。
                split()：分割字符串，返回一个分割后的字符串数组。
                getBytes()：返回字符串的 byte 类型数组。
                length()：返回字符串长度。
                toLowerCase()：将字符串转成小写字母。
                toUpperCase()：将字符串转成大写字符。
                substring()：截取字符串。
                equals()：字符串比较。

##### 抽象类必须要有抽象方法吗？
不需要，抽象类不一定非要有抽象方法。

                abstract class Cat {
                    public static void sayHi() {
                        System. out. println("hi~");
                    }
                }

##### 普通类和抽象类有哪些区别？
* 普通类不能包含抽象方法，抽象类可以包含抽象方法。
* 抽象类不能直接实例化，普通类可以直接实例化。

##### 抽象类能使用final修饰吗？
不能，定义抽象类就是让其他类继承的，如果定义为 final 该类就不能被继承，这样彼此就会产生矛盾，所以 final 不能修饰抽象类，如下图所示，
编辑器也会提示错误信息：

##### 接口和抽象类有什么区别？
* 实现：抽象类的子类使用 extends 来继承；接口必须使用 implements 来实现接口。
* 构造函数：抽象类可以有构造函数；接口不能有。
* 实现数量：类可以实现很多个接口；但是只能继承一个抽象类。
* 访问修饰符：接口中的方法默认使用 public 修饰；抽象类中的方法可以是任意访问修饰符。
