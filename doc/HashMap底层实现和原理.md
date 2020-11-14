#### HashMap\HashTable区别

            HashMap基于Map接口实现，元素以键值对的方式存储，并且允许使用null 建和null　值，因为key不允许重复，因此只能有一个键为null,
            另外HashMap不能保证放入元素的顺序，它是无序的，和放入的顺序并不能相同。
            HashMap是线程不安全的，多线程环境中推荐使用ConcurrentHashMap。
            HashMap的扩容操作是一项很耗时的任务，所以如果能估算Map的容量，最好给它一个默认初始值，避免进行多次扩容。
            HashMap的初始容量为16，Hashtable初始容量为11，两者的填充因子默认都是0.75。HashMap扩容时是当前容量翻倍即:capacity*2，Hashtable扩容时是容量翻倍+1即:capacity*2+1。
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
#### 底层实现
数组、双向链表、红黑树（jdk8），jdk8之前是数组、链表。 [数组](数组.md)：采用连续的存储单元、查询快、插入慢， [链表](链表.md)：不连续的存储单元、查询慢、删除和插入快， [红黑树](树.md)：查询快、插入慢
取key值通过哈希算法获取一个哈希值，hash(key.hashCode())%len，哈希值对数据长度取模得到存储位置，如果该位置已经存在值，则判断内容时否一样，不一样就放到链表里面。如果链表长度超过阈值8时，再jdk1.8中则自动转换为红黑树。
          
          
          HashMap()    //无参构造方法
          HashMap(int initialCapacity)  //指定初始容量的构造方法 
          HashMap(int initialCapacity, float loadFactor) //指定初始容量16和负载因子0.75
          HashMap(Map<? extends K,? extends V> m)  //指定集合，转化为HashMap

