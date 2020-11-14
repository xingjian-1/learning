#### 底层实现原理
###### HashMap在JDK1.8中采用数组、链表、红黑树的方式存储数据，首先通过key值用哈希算法获取一个哈希值，hash(key.hashCode())%len，哈希值对数组长度取模得到存储位置，如果该位置已经存在值，则判断内容是否一样，不一样就放到链表里面。如果链表长度超过阈值8时，则转换为红黑树。
###### [数组](/doc/数组.md)：采用连续的存储单元、查询快、插入慢， [链表](/doc/链表.md)：不连续的存储单元、查询慢、删除和插入快， [红黑树](/doc/树.md)：查询快、插入慢
#### HashMap的扩容机制
###### 那么hashmap什么时候进行扩容呢？当hashmap中的元素个数超过数组大小*loadFactor时，就会进行数组扩容，loadFactor的默认值为0.75，也就是说，默认情况下，数组大小为16，那么当hashmap中元素个数超过16*0.75=12的时候，就把数组的大小扩展为2*16=32，即扩大一倍，然后重新计算每个元素在数组中的位置，而这是一个非常消耗性能的操作，所以如果我们已经预知hashmap中元素的个数，那么预设元素的个数能够有效的提高hashmap的性能。比如说，我们有1000个元素new HashMap(1000), 但是理论上来讲new HashMap(1024)更合适，不过上面annegu已经说过，即使是1000，hashmap也自动会将其设置为1024。 但是new HashMap(1024)还不是更合适的，因为0.75*1000 < 1000, 也就是说为了让0.75 * size > 1000, 我们必须这样new HashMap(2048)才最合适，既考虑了&的问题，也避免了resize的问题。 
###### HashMap数组的初始容量16和负载因子为0.75。
#### HashMapJDK1.8之前并发下的死循环

#### HashMapJDK1.8的优化
###### 数据结构上的优化，由原本的数组+链表的结构引入了红黑树，当链表长度大于8时，整个数组长度大于64时链表转换为红黑树，反之链表长度小于6时再转换回来，好处是避免链表过长，降低查询复杂度，提升查询速度。
###### Hash冲突处理上的优化，原本hash冲突时在链表头部插入数据，现在改为尾部插入，好处是避免扩容后链表产生相对位置倒序，避免在并发环境下扩容产生循环链表，导致死循环。
###### 扩容寻址算法的优化，。。。。
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

