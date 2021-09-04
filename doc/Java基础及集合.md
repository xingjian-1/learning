#### Collection
Java容器分为Collection和Map两大类，都在Java.util包下面。
 `Collection`接口，提供了集合对象进行基本操作的通用方法， List、Set是它的子类。
 `Collections`是一个包装类，包含了很多静态方法，不能被实例化，就像一个工具类，比如提供的排序方法： Collections. sort(list)。（Jdk1.8中可以用，Jdk11可能作了改动）
 
           public interface Collection<E> extends Iterable<E> {}
           public abstract class AbstractCollection<E> implements Collection<E> {}
  
#### List 
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
 
 ArrayList扩容机制：
               
               /**
                * 默认初始容量大小
                */
               private static final int DEFAULT_CAPACITY = 10;
               private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
               /**
                *默认构造函数，使用初始容量10构造一个空列表(无参数构造)
                */
               public ArrayList() {
                   this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
               }
               /**
                * 带初始容量参数的构造函数。（用户自己指定容量）
                */
               public ArrayList(int initialCapacity) {
                   if (initialCapacity > 0) {//初始容量大于0
                       //创建initialCapacity大小的数组
                       this.elementData = new Object[initialCapacity];
                   } else if (initialCapacity == 0) {//初始容量等于0
                       //创建空数组
                       this.elementData = EMPTY_ELEMENTDATA;
                   } else {//初始容量小于0，抛出异常
                       throw new IllegalArgumentException("Illegal Capacity: "+
                                                          initialCapacity);
                   }
               }
               
              /**
               *如果指定的集合为null，throws NullPointerException。
               */
                public ArrayList(Collection<? extends E> c) {
                   elementData = c.toArray();
                   if ((size = elementData.length) != 0) {
                       // c.toArray might (incorrectly) not return Object[] (see 6260652)
                       if (elementData.getClass() != Object[].class)
                           elementData = Arrays.copyOf(elementData, size, Object[].class);
                   } else {
                       // replace with empty array.
                       this.elementData = EMPTY_ELEMENTDATA;
                   }
               }
               以无参数构造方法创建ArrayList时，初始化赋值的是一个空数组。对数组进行添加元素操作时，才分配容量。
               即向数组中添加第一个元素时，数组容量扩为10。
               /**
               * 要分配的最大数组大小
               */
              private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
              /**
               * ArrayList扩容的核心方法。
               */
              private void grow(int minCapacity) {
                  //oldCapacity为旧容量，newCapacity为新容量
                  int oldCapacity = elementData.length;
                  //将oldCapacity右移一位，其效果相当于oldCapacity/2，
                  //位运算的速度远远快于整除运算，将新容量更新为旧容量的1.5倍，
                  int newCapacity = oldCapacity + (oldCapacity >> 1);
                  //然后检查新容量是否大于最小需要容量，若还是小于最小需要容量，那么就把最小需要容量当作数组的新容量，
                  if (newCapacity - minCapacity < 0)
                      newCapacity = minCapacity;
                 //如果新容量大于MAX_ARRAY_SIZE,进入(执行) `hugeCapacity()` 方法来比较 minCapacity 和 MAX_ARRAY_SIZE，
                 //如果minCapacity大于最大容量，新容量则为`Integer.MAX_VALUE`，否则，新容量大小则为 MAX_ARRAY_SIZE 即为 `Integer.MAX_VALUE - 8`。
                  if (newCapacity - MAX_ARRAY_SIZE > 0)
                      newCapacity = hugeCapacity(minCapacity);
                  // minCapacity is usually close to size, so this is a win:
                  elementData = Arrays.copyOf(elementData, newCapacity);
              }
              int newCapacity = oldCapacity + (oldCapacity >> 1),ArrayList每次扩容之后容量都会变为原来的1.5倍左右
              oldCapacity为偶数就是1.5倍，否则是1.5倍左右）奇偶不同，比如 ：10+10/2 = 15, 33+33/2=49。如果是奇数的话会丢掉小数.
              
              private static int hugeCapacity(int minCapacity) {
                    if (minCapacity < 0) // overflow
                        throw new OutOfMemoryError();
                    //对minCapacity和MAX_ARRAY_SIZE进行比较
                    //若minCapacity大，将Integer.MAX_VALUE作为新数组的大小
                    //若MAX_ARRAY_SIZE大，将MAX_ARRAY_SIZE作为新数组的大小
                    //MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
                    return (minCapacity > MAX_ARRAY_SIZE) ?
                        Integer.MAX_VALUE :
                        MAX_ARRAY_SIZE;
                }
如果新容量大于 MAX_ARRAY_SIZE,进入(执行) hugeCapacity() 方法来比较 minCapacity 和 MAX_ARRAY_SIZE，如果minCapacity大于最大容量，则新容量则为Integer.MAX_VALUE，否则，新容量大小则为 MAX_ARRAY_SIZE 即为 Integer.MAX_VALUE - 8。

* LinkedList
           
           public abstract class AbstractSequentialList<E> extends AbstractList<E> {}
           public class LinkedList<E>extends AbstractSequentialList<E>
           implements List<E>, Deque<E>, Cloneable, java.io.Serializable
* Vector
           
           public class Vector<E>extends AbstractList<E>
           implements List<E>, RandomAccess, Cloneable, java.io.Serializable
* Stack          
           
           public class Stack<E> extends Vector<E> {}


#### Set
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
                    
#### Map 

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
 
HashMap基于Map接口实现，元素以键值对的方式存储，并且允许使用null键和null值，因为key不允许重复，因此只能有一个键为null,另外HashMap不能保证放入元素的顺序，它是无序的，和放入的顺序并不能相同。
HashMap是线程不安全的，多线程环境中推荐使用ConcurrentHashMap。HashMap的扩容操作是一项很耗时的任务，所以如果能估算Map的容量，最好给它一个默认初始值，避免进行多次扩容。HashMap的初始容量为16，Hashtable初始容量为11，两者的填充因子默认都是0.75。HashMap扩容时是当前容量翻倍即:capacity*2，Hashtable扩容时是容量翻倍+1即:capacity*2+1。
            Hashtable计算hash是直接使用key的hashcode对table数组的长度直接进行取模：
            
            int hash = key.hashCode();
            int index = (hash & 0x7FFFFFFF) % tab.length;
            
            HashMap计算hash对key的hashcode进行了二次hash，以获得更好的散列值，然后对table数组长度取摸：
            int hash = hash(key.hashCode());
            int i = indexFor(hash, table.length);
            static int hash(int h) {
                    h ^= (h >>> 20) ^ (h >>> 12);
                    return h ^ (h >>> 7) ^ (h >>> 4);
                }

             static int indexFor(int h, int length) {
                    return h & (length-1);
             } 
             
HashMap实现原理：jdk1.8中HashMap存储结构是：数组+链表+红黑树，HashMap基于Hash算法实现的，我们通过put(key,value)存储，get(key)来获取。当传入 key时，HashMap会根据key.hashCode()计算出hash值，根据hash值定位到具体的数组存储位置，如果相同的位置已经存在hash值，称为hash冲突，这里处理hash冲突的办法是链表法，判断内容是否相同，不相同放到链表的尾部，当链表长度大于8时会转换为红黑树。<br>
HashMap扩容机制：hashmap什么时候进行扩容呢？当hashmap中的元素个数超过数组大小*loadFactor时，就会进行数组扩容，loadFactor的默认值为0.75，也就是说，默认情况下，数组大小为16，当hashmap中元素个数超过16*0.75=12的时候，就把数组的大小扩展为2*16=32，即扩大一倍，然后重新计算每个元素在数组中的位置，而这是一个非常消耗性能的操作，所以如果我们已经预知hashmap中元素的个数，那么预设元素的个数能够有效的提高hashmap的性能。比如说，我们有1000个元素new HashMap(1000), 但是理论上来讲new HashMap(1024)更合适，不过上面annegu已经说过，即使是1000，hashmap也自动会将其设置为1024。 但是new HashMap(1024)还不是更合适的，因为0.75*1000 < 1000, 也就是说为了让0.75 * size > 1000, 我们必须这样new HashMap(2048)才最合适，既考虑了&的问题，也避免了resize的问题。 
HashMap数组的初始容量16和负载因子为0.75。<br>
HashMap1.8之前并发下的死循环：在Jdk1.7中，并发扩容时，同时执行transfer方法，如果原始链表相邻的两个元素，扩容后仍是相邻的两个元素，由于采用了头插入，会造成两个元素形成互为首尾,形成死循环。举例：
            
            头插法：相当于后来居上， 后面的结点不断往前插，而最后创建的结点在第一个结点处， 第一个创建的结点变成了尾结点。
            1.hashmap的初始大小为2^n（n=1）, 那么hashmap的数组size是2， 插入两个元素3和7，利用取模算法（3%2=1，7%2=1）两个元素的位置都在数组下标1上，
            链表头部依次插入7和3.
            2.两个线程对hashmap进行扩容(调用transfer)7%4=3，3%4=3， 假设线程一执行到获取第一个元素3，CPU调度到第二个线程, 第二个线程完成了全部的扩容操作,由于扩容采用了头插法，元素7插入到元             素3之前，并作为链表的第一个元素。此刻时间片正好用完，内部的table还没有设置成新的newTable,数组下标为3的位置上数值排列顺序是：7在前 3在后
            3.第一个线程开始执行，在数组下标为3的位置上数值排列的顺序是：3在前 7在后，形成死循环
            所以在并发的情况，发生扩容时，可能会产生循环链表，在执行get的时候，会触发死循环，引起CPU的100%问题，所以一定要避免在并发环境下使用HashMap。

            曾经有人把这个问题报给了Sun，不过Sun不认为这是一个bug，因为在HashMap本来就不支持多线程使用，要并发就用ConcurrentHashmap。<br>
HashMapJDK1.8的优化:
1.数据结构上的优化<br>
由原本的数组+链表的结构引入了红黑树，当链表长度大于8时，整个数组长度大于64时链表转换为红黑树，反之链表长度小于6时再转换回来，好处是避免链表过长，降低查询复杂度，提升查询速度。
原本hash冲突时在链表头部插入数据，现在改为尾部插入，好处是避免扩容后链表产生相对位置倒序，避免在并发环境下扩容产生循环链表，导致死循环。<br>
2.Hash碰撞问题解决方案的优化 <br>
当key的hash碰撞频率高，导致链表内的数据过多，影响查询效率， 时间复杂度为O(n)。hashmap采用的就是链地址法（拉链法），jdk1.7中，当冲突时，在冲突的地址上生成一个链表，将冲突的元素的key，通过equals进行比较，相同即覆盖，不同则添加到链表上，此时如果链表过长，效率就会大大降低，查找和添加操作的时间复杂度都为O(n)；但是在jdk1.8中如果链表长度大于8，链表就会转化为红黑树，时间复杂度也降为了O(logn)，性能得到了很大的优化。

            链表法就是将相同hash值的对象组织成一个链表放在hash值对应的槽位。
            开放地址法是通过一个探测算法，当某个槽位已经被占据的情况下继续查找下一个可以使用的槽位。java.util.HashMap采用的链表法的方式，链表是单向链表
3.Hash值算法的优化 <br>
对每个hash值，在它的低16位中，让高低16位进行异或，让它的低16位同时保持了高低16位的特征，尽量避免一些hash值后续出现冲突，大家可能会进入数组的同一位置

            static final int hash(Object key) {
                    int h;
                    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
                }
                有一个key的Hash值：1111 1111 1111 1111 1111 1010 0111 1100
                h >>> 16 //表示对该hash值右移16位
                右移后的结果为：0000 0000 0000 0000 1111 1111 1111 1111
                两个值进行异或：
                Hash_1: 1111 1111 1111 1111 1111 1010 0111 1100
                Hash_2: 0000 0000 0000 0000 1111 1111 1111 1111
                =====>: 1111 1111 1111 1111 0000 0101 1000 0011 =====> 转为10进制int值，这个值就是这个key的hash值
                
3.寻址算法的优化 <br>
用与运算替代取模，提升性能。

            (p = tab[i = (n - 1) & hash] 
            // (n-1) & hash ==> 数组里的一个位置
            hash & (n-1) 效果是跟hash对n取模是一样的，但是与运算的性能要比hash对n取模要高很多。
            数组的长度会一直是2的n次方，只要他保持数组长度是2的n次方。
            1.寻址为什么不用取模，对于上面寻址算法，由于计算机对比取模，与运算会更快。所以为了效率，HashMap中规定了哈希表长度为2的k次方，
            而2^k-1转为二进制就是k个连续的1，那么hash& (k个连续的1) 返回的就是hash的低k个位，该计算结果范围刚好就是0到2^k-1，即0到length - 1，跟取模结果一样
            2.为什么不直接用 hashCode()而是用它的高16位进行异或计算新hash值？int类型占32位，可以表示2^32种数（范围：-2^31 到 2^31-1），而哈希表长度一般不大，
            在HashMap中哈希表的初始化长度是16（HashMap 中的 DEFAULT_INITIAL_CAPACITY），如果直接用hashCode来寻址，那么相当于只有低4位有效，其他高位不会有影响。
            这样假如几个hashCode分别是210、220、2^30，那么寻址结果index就会一样而发生冲突，所以哈希表就不均匀分布了。

4.扩容机制的优化
HashMap底层是一个数组，当这个数组满了之后，他就会自动进行扩容，变成一个更大数组.
* ConcurrentHashMap
ConcurrentHashMap采用锁分段技术：首先将数据分成一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问。 ConcurrentHashMap提供了与Hashtable和SynchronizedMap不同的锁机制。Hashtable中采用的锁机制是一次锁住整个hash表，从而在同一时刻只能由一个线程对其进行操作；而ConcurrentHashMap中则是一次锁住一个桶。ConcurrentHashMap默认将hash表分为16个桶，诸如get、put、remove等常用操作只锁住当前需要用到的桶。原来只能一个线程进入，现在却能同时有16个写线程执行，并发性能的提升是显而易见的。<br>


           public class ConcurrentHashMap<K,V> extends AbstractMap<K,V>
           implements ConcurrentMap<K,V>, Serializable {}//jdk1.8版本这个类在Java.util.concurrent包里面
* LinkedHashMap

           public class LinkedHashMap<K,V> extends HashMap<K,V>
           implements Map<K,V>
* TreeMap  
在非多线程环境中如果有插入、删除、定位一个元素这类操作，HashMap的插入会更快，如果输出的结果要有序，那就选TreeMap，TreeMap会对结果进行有序的遍历。

           public class TreeMap<K,V> extends AbstractMap<K,V>
           implements NavigableMap<K,V>, Cloneable, java.io.Serializable{}
          
#### 迭代器Iterator
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
#### 关键字、操作类等
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
* 泛型：泛型就是可以适应不同的类型，这种参数类型可以用在类、接口和方法的创建中，分别称为泛型类、泛型接口、泛型方法。保证了类型的安全性：泛型约束了变量的类型，保证了类型的安全性。例如List<int>和ArrayList。List<int>集合只能加入int类型的变量，ArrayList可以Add任何常用类型，编译的时候不会提示错误。泛型能够省去类型强制转换。提高方法、算法的重用性。
                    
                  //泛型类
                  public class GenericClass<T> {
                    private T value;
                    public GenericClass(T value) {
                        this.value = value;
                    }
                    public T getValue() {
                        return value;
                    }
                    public void setValue(T value) {
                        this.value = value;
                    }
                   }
                   
                  //泛型接口
                  public interface GenericInterface<T> {
                   void show(T value);
                 }
                 
                 //泛型方法
                public class GenericFun {
                    public void show(String value) { }
                    public void show(Integer value) { }
                }
