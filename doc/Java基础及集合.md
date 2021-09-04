#### Collection
Java容器分为Collection和Map两大类，都在Java.util包下面。
 `Collection`接口，提供了集合对象进行基本操作的通用方法， List、Set是它的子类。
 `Collections`是一个包装类，包含了很多静态方法，不能被实例化，就像一个工具类，比如提供的排序方法： Collections. sort(list)。（Jdk1.8中可以用，Jdk11可能作了改动）
 
           public interface Collection<E> extends Iterable<E> {}
           public abstract class AbstractCollection<E> implements Collection<E> {}
  
##### List 
List特点是元素有序，且可重复。实现的常用集合类有ArrayList、LinkedList，和Vector（线程安全）。

           public interface List<E> extends Collection<E> 
           public abstract class AbstractList<E> extends AbstractCollection<E> implements List<E>{}
           
* ArrayList
ArrayList的数据结构是数组，LinkedList是双向链表的数据结构实现，查询时LinkedList需要移动指针从前往后依次查找，ArrayList根据数组下标直接查找更快一些，所以ArrayList比LinkedList查询效率高，ArrayList底层是数组结构来实现的，数组结构采用连续的存储单元，增删操作会涉及到数组的移动，链表采用非连续的存储单元，所以效率要高于ArrayList。在需要频繁读取集合中的元素时，使用ArrayList，在插入和删除操作时，使用LinkedList。<br>
线程安全：Vector使用了Synchronized来实现线程同步，是线程安全的，而ArrayList是非线程安全的。<br>
性能：ArrayList在性能方面要优于Vector。<br>
扩容：ArrayList和Vector都会根据实际的需要动态的调整容量，只不过在Vector扩容每次会增加1倍，而ArrayList只会增加50%。<br>

           public class ArrayList<E> extends AbstractList<E>
           implements List<E>, RandomAccess, Cloneable, java.io.Serializable{}
           

* LinkedList
           
           public abstract class AbstractSequentialList<E> extends AbstractList<E> {}
           public class LinkedList<E>extends AbstractSequentialList<E>
           implements List<E>, Deque<E>, Cloneable, java.io.Serializable
* Vector
           
           public class Vector<E>extends AbstractList<E>
           implements List<E>, RandomAccess, Cloneable, java.io.Serializable
* Stack          
           
           public class Stack<E> extends Vector<E> {}


##### Set
Set特点是元素无序，不可重复。实现的常用集合类有HashSet，LinkedHashSet，TreeSet（可排序）。

           public interface Set<E> extends Collection<E> {}
           public abstract class AbstractSet<E> extends AbstractCollection<E> implements Set<E> {}
* HashSet
    
           public class HashSet<E> extends AbstractSet<E>
           implements Set<E>, Cloneable, java.io.Serializable{}
       
 HashSet实现原理：HashSet是基于HashMap实现的，HashSet底层使用HashMap来保存所有元素，实现比较简单，相关HashSet的操作，基本上都是直接调用底层HashMap的相关方法来完成，HashSet不允许重复的值。
* LinkedHashSet

           public class LinkedHashSet<E> extends HashSet<E>
           implements Set<E>, Cloneable, java.io.Serializable {}
* TreeSet

           public class TreeSet<E> extends AbstractSet<E>
           implements NavigableSet<E>, Cloneable, java.io.Serializable{}
                    
##### Map 

           public interface Map<K,V> {}
           public abstract class AbstractMap<K,V> implements Map<K,V> {}
           public abstract class Dictionary<K,V> {
* Hashtable

           public class Hashtable<K,V> extends Dictionary<K,V>
           implements Map<K,V>, Cloneable, java.io.Serializable {}
           HashMap和HashTable区别:
           1.存储：HashMap允许key和value为null，Hashtable不允许，会抛出NullPointerException异常。
           2.线程安全：Hashtable是线程安全的，而HashMap是非线程安全的，效率上高于Hashtable。如果多线程下建议使用ConcurrentHashMap替代Hashtable。
           3.HashMap中hash数组的默认大小是16，其大小无论初始值还是扩容都是2的指数。HashTable中hash数组默认大小是11，增加的方式是oldCapacity*2+1。 
           4.HashMap是Map接口的实现，Hashtable 继承于Dictionary类。
           HashMap实现同步方式：Map m = Collections.synchronizeMap(hashMap);
* HashMap

           public class HashMap<K,V> extends AbstractMap<K,V>
           implements Map<K,V>, Cloneable, Serializable {}
           
HashMap实现原理：jdk1.8中HashMap存储结构是：数组+链表+红黑树，HashMap基于Hash算法实现的，我们通过put(key,value)存储，get(key)来获取。当传入 key时，HashMap会根据key.hashCode()计算出hash值，根据hash值定位到具体的数组存储位置，如果相同的位置已经存在相同的hash值，称为hash冲突，内容是否相同，不相同放到链表的尾部，当链表长度大于8时会转换为红黑树。         
* ConcurrentHashMap
ConcurrentHashMap采用锁分段技术：首先将数据分成一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问。 ConcurrentHashMap提供了与Hashtable和SynchronizedMap不同的锁机制。Hashtable中采用的锁机制是一次锁住整个hash表，从而在同一时刻只能由一个线程对其进行操作；而ConcurrentHashMap中则是一次锁住一个桶。ConcurrentHashMap默认将hash表分为16个桶，诸如get、put、remove等常用操作只锁住当前需要用到的桶。原来只能一个线程进入，现在却能同时有16个写线程执行，并发性能的提升是显而易见的。

           public class ConcurrentHashMap<K,V> extends AbstractMap<K,V>
           implements ConcurrentMap<K,V>, Serializable {}//jdk1.8版本这个类在Java.util.concurrent包里面
* LinkedHashMap

           public class LinkedHashMap<K,V> extends HashMap<K,V>
           implements Map<K,V>
* TreeMap  
在非多线程环境中如果有插入、删除、定位一个元素这类操作，HashMap的插入会更快，如果输出的结果要有序，那就选TreeMap，TreeMap会对结果进行有序的遍历。

           public class TreeMap<K,V> extends AbstractMap<K,V>
           implements NavigableMap<K,V>, Cloneable, java.io.Serializable{}
          
##### 迭代器Iterator
Iterator 接口提供遍历任何Collection的接口。我们可以从一个Collection中使用迭代器方法来获取迭代器实例。迭代器取代了Java集合框架中的Enumeration，迭代器允许调用者在迭代过程中移除元素。
* Iterator怎么使用？有什么特点？
        
                    List<String> list = new ArrayList<>();
                            Iterator<String> it = list. iterator();
                            while(it. hasNext()){
                              String obj = it. next();
                              System. out. println(obj);
                            }
                    Iterator 的特点是更加安全，因为它可以确保，在当前遍历的集合元素被更改的时候，
                    就会抛出ConcurrentModificationException异常。

* Iterator和ListIterator区别

                  Iterator可以遍历Set和List集合，而ListIterator只能遍历List。
                  Iterator只能单向遍历，而ListIterator可以双向遍历（向前/后遍历）。
                  ListIterator 从 Iterator 接口继承，然后添加了一些额外的功能，比如添加一个元素、替换一个元素、获取前面或后面元素的索引位置。
* 怎么确保一个集合不能被修改？

                   可以使用 Collections. unmodifiableCollection(Collection c) 方法来创建一个只读集合，这样改变集合的任何操作都会抛出
                   Java. lang. UnsupportedOperationException 异常。
                   示例代码如下：
                    List<String> list = new ArrayList<>();
                            list. add("x");
                            Collection<String> clist = Collections. unmodifiableCollection(list);
                            clist. add("y"); // 运行时此行报错
                            System. out. println(list. size());
##### 关键字、操作类等
* final关键字：final修饰的类叫最终类，该类不能被继承。final 修饰的方法不能被重写。final 修饰的变量叫常量，常量必须初始化，初始化之后值就不能被修改。
* 操作字符串的类有：String、StringBuffer、StringBuilder。String 和 StringBuffer、StringBuilder 的区别在于 String 声明的是不可变的对象，每次操作都会生成新的 String 对象，然后将指针指向新的 String 对象，而 StringBuffer、StringBuilder 可以在原有对象的基础上进行操作，所以在经常改变字符串内容的情况下最好不要使用 String。
StringBuffer 和 StringBuilder 最大的区别在于，StringBuffer 是线程安全的，而 StringBuilder 是非线程安全的，但 StringBuilder 的性能却高于 
StringBuffer，所以在单线程环境下推荐使用 StringBuilder，多线程环境下推荐使用 StringBuffer。
* String str="i"与 String str=new String("i")：String str="i"的方式，Java 虚拟机会将其分配到常量池中；而 String str=new String("i") 则会被分到堆内存中。
* 字符串反转：

                    使用 StringBuilder 或者 stringBuffer 的 reverse() 方法。
                    // StringBuffer reverse
                    StringBuffer stringBuffer = new StringBuffer();
                    stringBuffer. append("abcdefg");
                    System. out. println(stringBuffer. reverse()); // gfedcba
                    // StringBuilder reverse
                    StringBuilder stringBuilder = new StringBuilder();
                    stringBuilder. append("abcdefg");
                    System. out. println(stringBuilder. reverse()); // gfedcba
* String类的常用方法:

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
* 普通类和抽象类区别：普通类不能包含抽象方法，抽象类可以包含抽象方法，抽象类不能直接实例化，普通类可以直接实例化。
* 抽象类是否能使用final修饰：不能，定义抽象类就是让其他类继承的，如果定义为 final该类就不能被继承，这样彼此就会产生矛盾，所以final不能修饰抽象类。
* 接口和抽象类区别：

                    实现：抽象类的子类使用 extends 来继承；接口必须使用 implements 来实现接口。
                    构造函数：抽象类可以有构造函数；接口不能有。
                    实现数量：类可以实现很多个接口；但是只能继承一个抽象类。
                    访问修饰符：接口中的方法默认使用 public 修饰；抽象类中的方法可以是任意访问修饰符。
