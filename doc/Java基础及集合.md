▶ JDK 和 JRE 有什么区别？
JDK：Java Development Kit 的简称，Java 开发工具包，提供了 Java 的开发环境和运行环境。
JRE：Java Runtime Environment 的简称，Java 运行环境，为 Java 的运行提供了所需环境。
具体来说 JDK 其实包含了 JRE，同时还包含了编译 Java 源码的编译器 Javac，还包含了很多 Java 程序调试和分析的工具。简单来说：如果你需要运行 Java 程序，
只需安装 JRE 就可以了，如果你需要编写 Java 程序，需要安装 JDK。
[HashMap底层实现和原理](./doc/HashMap底层实现和原理.md)
▶ == 和 equals 的区别是什么？
基本类型：比较的是值是否相同；
引用类型：比较的是引用是否相同；
代码示例：
String x = "string";
String y = "string";
String z = new String("string");
System.out.println(x==y); // true
System.out.println(x==z); // false
System.out.println(x.equals(y)); // true
System.out.println(x.equals(z)); // true
代码解读：因为 x 和 y 指向的是同一个引用，所以 == 也是 true，而 new String()方法则重写开辟了内存空间，所以 == 结果为 false，而 equals 
比较的一直是值，所以结果都为 true。
equals 解读
equals 本质上就是 ==，只不过 String 和 Integer 等重写了 equals 方法，把它变成了值比较。看下面的代码就明白了。
首先来看默认情况下 equals 比较一个有相同值的对象，代码如下：
class Cat {
    public Cat(String name) {
        this.name = name;
    }
    private String name;
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
}
Cat c1 = new Cat("王磊");
Cat c2 = new Cat("王磊");
System.out.println(c1.equals(c2)); // false
输出结果出乎我们的意料，竟然是 false？这是怎么回事，看了 equals 源码就知道了，源码如下：
public boolean equals(Object obj) {
        return (this == obj);
}
原来 equals 本质上就是 ==。
那问题来了，两个相同值的 String 对象，为什么返回的是 true？代码如下：
String s1 = new String("老王");
String s2 = new String("老王");
System.out.println(s1.equals(s2)); // true
同样的，当我们进入 String 的 equals 方法，找到了答案，代码如下：
public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
原来是 String 重写了 Object 的 equals 方法，把引用比较改成了值比较。
总结 ：== 对于基本类型来说是值比较，对于引用类型来说是比较的是引用；而 equals 默认情况下是引用比较，只是很多类重新了 equals 方法，
比如 String、Integer 等把它变成了值比较，所以一般情况下 equals 比较的是值是否相等。

▶ 两个对象的 hashCode() 相同，则 equals() 也一定为 true，对吗？
不对，两个对象的 hashCode() 相同，equals() 不一定 true。
代码示例：
String str1 = "通话";
String str2 = "重地";
System. out. println(String. format("str1：%d | str2：%d",  str1. hashCode(),str2. hashCode()));
System. out. println(str1. equals(str2));
执行的结果：
str1：1179395 | str2：1179395
false
代码解读：很显然“通话”和“重地”的 hashCode() 相同，然而 equals() 则为 false，因为在散列表中，hashCode() 相等即两个键值对的哈希值相等，然而哈希值相等，并不一定能得出键值对相等。

▶ final 在 Java 中有什么作用？
final 修饰的类叫最终类，该类不能被继承。
final 修饰的方法不能被重写。
final 修饰的变量叫常量，常量必须初始化，初始化之后值就不能被修改。

▶ Java 中的 Math. round(-1. 5) 等于多少？
等于 -1，因为在数轴上取值时，中间值（0.5）向右取整，所以正 0.5 是往上取整，负 0.5 是直接舍弃。

▶ String 属于基础的数据类型吗？
String 不属于基础类型，基础类型有 8 种：byte、boolean、char、short、int、float、long、double，而 String 属于对象。

▶ Java 中操作字符串都有哪些类？它们之间有什么区别？
操作字符串的类有：String、StringBuffer、StringBuilder。
String 和 StringBuffer、StringBuilder 的区别在于 String 声明的是不可变的对象，每次操作都会生成新的 String 对象，然后将指针指向新的 String 对象，
而 StringBuffer、StringBuilder 可以在原有对象的基础上进行操作，所以在经常改变字符串内容的情况下最好不要使用 String。
StringBuffer 和 StringBuilder 最大的区别在于，StringBuffer 是线程安全的，而 StringBuilder 是非线程安全的，但 StringBuilder 的性能却高于 
StringBuffer，所以在单线程环境下推荐使用 StringBuilder，多线程环境下推荐使用 StringBuffer。

▶ String str="i"与 String str=new String("i")一样吗？
不一样，因为内存的分配方式不一样。String str="i"的方式，Java 虚拟机会将其分配到常量池中；而 String str=new String("i") 则会被分到堆内存中。

▶ 如何将字符串反转？
使用 StringBuilder 或者 stringBuffer 的 reverse() 方法。
示例代码：
// StringBuffer reverse
StringBuffer stringBuffer = new StringBuffer();
stringBuffer. append("abcdefg");
System. out. println(stringBuffer. reverse()); // gfedcba
// StringBuilder reverse
StringBuilder stringBuilder = new StringBuilder();
stringBuilder. append("abcdefg");
System. out. println(stringBuilder. reverse()); // gfedcba

▶ String 类的常用方法都有那些？
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

▶ 抽象类必须要有抽象方法吗？
不需要，抽象类不一定非要有抽象方法。
示例代码：
abstract class Cat {
    public static void sayHi() {
        System. out. println("hi~");
    }
}
上面代码，抽象类并没有抽象方法但完全可以正常运行。

▶ 普通类和抽象类有哪些区别？
普通类不能包含抽象方法，抽象类可以包含抽象方法。
抽象类不能直接实例化，普通类可以直接实例化。

▶ 抽象类能使用 final 修饰吗？
不能，定义抽象类就是让其他类继承的，如果定义为 final 该类就不能被继承，这样彼此就会产生矛盾，所以 final 不能修饰抽象类，如下图所示，
编辑器也会提示错误信息：

▶ 接口和抽象类有什么区别？
实现：抽象类的子类使用 extends 来继承；接口必须使用 implements 来实现接口。
构造函数：抽象类可以有构造函数；接口不能有。
实现数量：类可以实现很多个接口；但是只能继承一个抽象类。
访问修饰符：接口中的方法默认使用 public 修饰；抽象类中的方法可以是任意访问修饰符。

▶ Java 中 IO 流分为几种？
按功能来分：输入流（input）、输出流（output）。
按类型来分：字节流和字符流。
字节流和字符流的区别是：字节流按 8 位传输以字节为单位输入输出数据，字符流按 16 位传输以字符为单位输入输出数据。

▶ BIO、NIO、AIO 有什么区别？
BIO：Block IO 同步阻塞式 IO，就是我们平常使用的传统 IO，它的特点是模式简单使用方便，并发处理能力低。
NIO：Non IO 同步非阻塞 IO，是传统 IO 的升级，客户端和服务器端通过 Channel（通道）通讯，实现了多路复用。
AIO：Asynchronous IO 是 NIO 的升级，也叫 NIO2，实现了异步非堵塞 IO ，异步 IO 的操作基于事件和回调机制。
17. Files的常用方法都有哪些？
Files. exists()：检测文件路径是否存在。
Files. createFile()：创建文件。
Files. createDirectory()：创建文件夹。
Files. delete()：删除一个文件或目录。
Files. copy()：复制文件。
Files. move()：移动文件。
Files. size()：查看文件个数。
Files. read()：读取文件。
Files. write()：写入文件。

▶ Java 容器都有哪些？
Java 容器分为 Collection 和 Map 两大类，其下又有很多子类，如下所示：
Collection
List
ArrayList
LinkedList
Vector
Stack
Set
HashSet
LinkedHashSet
TreeSet
Map
HashMap
LinkedHashMap
TreeMap
ConcurrentHashMap
Hashtable

▶ Collection 和 Collections 有什么区别？
Collection 是一个集合接口，它提供了对集合对象进行基本操作的通用接口方法，所有集合都是它的子类，比如 List、Set 等。
Collections 是一个包装类，包含了很多静态方法，不能被实例化，就像一个工具类，比如提供的排序方法： Collections. sort(list)。

▶ List、Set、Map 之间的区别是什么？
List、Set、Map 的区别主要体现在两个方面：元素是否有序、是否允许元素重复。

▶ HashMap 和 Hashtable 有什么区别？
存储：HashMap 运行 key 和 value 为 null，而 Hashtable 不允许。
线程安全：Hashtable 是线程安全的，而 HashMap 是非线程安全的。
推荐使用：在 Hashtable 的类注释可以看到，Hashtable 是保留类不建议使用，推荐在单线程环境下使用 HashMap 替代，
如果需要多线程使用则用 ConcurrentHashMap 替代。

▶ 如何决定使用 HashMap 还是 TreeMap？
对于在 Map 中插入、删除、定位一个元素这类操作，HashMap 是最好的选择，因为相对而言 HashMap 的插入会更快，但如果你要对一个 key 集合进行有序的遍历，那 TreeMap 是更好的选择。

▶ 说一下 HashMap 的实现原理？
HashMap 基于 Hash 算法实现的，我们通过 put(key,value)存储，get(key)来获取。当传入 key 时，HashMap 会根据 key. hashCode() 计算出 hash 值，根据 hash 值将 value 保存在 bucket 里。当计算出的 hash 值相同时，我们称之为 hash 冲突，HashMap 的做法是用链表和红黑树存储相同 hash 值的 value。当 hash 冲突的个数比较少时，使用链表否则使用红黑树。

▶ 说一下 HashSet 的实现原理？
HashSet 是基于 HashMap 实现的，HashSet 底层使用 HashMap 来保存所有元素，因此 HashSet 的实现比较简单，相关 HashSet 的操作，基本上都是直接调用底层 HashMap 的相关方法来完成，HashSet 不允许重复的值。

▶ ArrayList 和 LinkedList 的区别是什么？
数据结构实现：ArrayList 是动态数组的数据结构实现，而 LinkedList 是双向链表的数据结构实现。
随机访问效率：ArrayList 比 LinkedList 在随机访问的时候效率要高，因为 LinkedList 是线性的数据存储方式，所以需要移动指针从前往后依次查找。
增加和删除效率：在非首尾的增加和删除操作，LinkedList 要比 ArrayList 效率要高，因为 ArrayList 增删操作要影响数组内的其他数据的下标。
综合来说，在需要频繁读取集合中的元素时，更推荐使用 ArrayList，而在插入和删除操作较多时，更推荐使用 LinkedList。

▶如何实现数组和 List 之间的转换？
数组转 List：使用 Arrays. asList(array) 进行转换。
List 转数组：使用 List 自带的 toArray() 方法。
代码示例：
// list to array
List<String> list = new ArrayList<String>();
list. add("王磊");
list. add("的博客");
list. toArray();
// array to list
String[] array = new String[]{"王磊","的博客"};
Arrays. asList(array);
▶ ArrayList 和 Vector 的区别是什么？
线程安全：Vector 使用了 Synchronized 来实现线程同步，是线程安全的，而 ArrayList 是非线程安全的。
性能：ArrayList 在性能方面要优于 Vector。
扩容：ArrayList 和 Vector 都会根据实际的需要动态的调整容量，只不过在 Vector 扩容每次会增加 1 倍，而 ArrayList 只会增加 50%。
▶ Array 和 ArrayList 有何区别？
Array 可以存储基本数据类型和对象，ArrayList 只能存储对象。
Array 是指定固定大小的，而 ArrayList 大小是自动扩展的。
Array 内置方法没有 ArrayList 多，比如 addAll、removeAll、iteration 等方法只有 ArrayList 有。
▶ 在 Queue 中 poll()和 remove()有什么区别？
相同点：都是返回第一个元素，并在队列中删除返回的对象。
不同点：如果没有元素 poll()会返回 null，而 remove()会直接抛出 NoSuchElementException 异常。
代码示例：
Queue<String> queue = new LinkedList<String>();
queue. offer("string"); // add
System. out. println(queue. poll());
System. out. println(queue. remove());
System. out. println(queue. size());
▶ 哪些集合类是线程安全的？
Vector、Hashtable、Stack 都是线程安全的，而像 HashMap 则是非线程安全的，不过在 JDK 1.5 之后随着 Java. util. concurrent 并发包的出现，它们也有了自己对应的线程安全类，比如 HashMap 对应的线程安全类就是 ConcurrentHashMap。

▶迭代器 Iterator 是什么？
Iterator 接口提供遍历任何 Collection 的接口。我们可以从一个 Collection 中使用迭代器方法来获取迭代器实例。迭代器取代了 Java 集合框架中的 Enumeration，迭代器允许调用者在迭代过程中移除元素。

▶Iterator 怎么使用？有什么特点？
Iterator 使用代码如下：
List<String> list = new ArrayList<>();
Iterator<String> it = list. iterator();
while(it. hasNext()){
  String obj = it. next();
  System. out. println(obj);
}
Iterator 的特点是更加安全，因为它可以确保，在当前遍历的集合元素被更改的时候，就会抛出 ConcurrentModificationException 异常。

▶ Iterator 和 ListIterator 有什么区别？
Iterator 可以遍历 Set 和 List 集合，而 ListIterator 只能遍历 List。
Iterator 只能单向遍历，而 ListIterator 可以双向遍历（向前/后遍历）。
ListIterator 从 Iterator 接口继承，然后添加了一些额外的功能，比如添加一个元素、替换一个元素、获取前面或后面元素的索引位置。
▶ 怎么确保一个集合不能被修改？
可以使用 Collections. unmodifiableCollection(Collection c) 方法来创建一个只读集合，这样改变集合的任何操作都会抛出
Java. lang. UnsupportedOperationException 异常。
示例代码如下：
List<String> list = new ArrayList<>();
list. add("x");
Collection<String> clist = Collections. unmodifiableCollection(list);
clist. add("y"); // 运行时此行报错
System. out. println(list. size());
