#### 底层实现原理
HashMap在JDK1.8中采用数组、链表、红黑树的方式存储数据，首先通过key值用哈希算法获取一个哈希值，hash(key.hashCode())%len，哈希值对数组长度取模得到存储位置，如果该位置已经存在值，则判断内容是否一样，不一样就放到链表里面。如果链表长度超过阈值8时，则转换为红黑树。[数组](/doc/数组.md)：采用连续的存储单元、查询快、插入慢， [链表](/doc/链表.md)：不连续的存储单元、查询慢、删除和插入快， [红黑树](/doc/树.md)：查询快、插入慢
#### HashMap的扩容机制
hashmap什么时候进行扩容呢？当hashmap中的元素个数超过数组大小*loadFactor时，就会进行数组扩容，loadFactor的默认值为0.75，也就是说，默认情况下，数组大小为16，当hashmap中元素个数超过16*0.75=12的时候，就把数组的大小扩展为2*16=32，即扩大一倍，然后重新计算每个元素在数组中的位置，而这是一个非常消耗性能的操作，所以如果我们已经预知hashmap中元素的个数，那么预设元素的个数能够有效的提高hashmap的性能。比如说，我们有1000个元素new HashMap(1000), 但是理论上来讲new HashMap(1024)更合适，不过上面annegu已经说过，即使是1000，hashmap也自动会将其设置为1024。 但是new HashMap(1024)还不是更合适的，因为0.75*1000 < 1000, 也就是说为了让0.75 * size > 1000, 我们必须这样new HashMap(2048)才最合适，既考虑了&的问题，也避免了resize的问题。 
HashMap数组的初始容量16和负载因子为0.75。
#### HashMapJDK1.8之前并发下的死循环

#### HashMapJDK1.8的优化
##### 数据结构上的优化
由原本的数组+链表的结构引入了红黑树，当链表长度大于8时，整个数组长度大于64时链表转换为红黑树，反之链表长度小于6时再转换回来，好处是避免链表过长，降低查询复杂度，提升查询速度。
原本hash冲突时在链表头部插入数据，现在改为尾部插入，好处是避免扩容后链表产生相对位置倒序，避免在并发环境下扩容产生循环链表，导致死循环
##### Hash碰撞问题解决方案的优化
hashmap采用的就是链地址法（拉链法），jdk1.7中，当冲突时，在冲突的地址上生成一个链表，将冲突的元素的key，通过equals进行比较，相同即覆盖，不同则添加到链表上，此时如果链表过长，效率就会大大降低，查找和添加操作的时间复杂度都为O(n)；但是在jdk1.8中如果链表长度大于8，链表就会转化为红黑树，时间复杂度也降为了O(logn)，性能得到了很大的优化。
* 链表法就是将相同hash值的对象组织成一个链表放在hash值对应的槽位。
* 开放地址法是通过一个探测算法，当某个槽位已经被占据的情况下继续查找下一个可以使用的槽位。java.util.HashMap采用的链表法的方式，链表是单向链表
##### Hash值算法的优化
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
                
##### 寻址算法的优化
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

##### 扩容机制的优化
            
            void resize(int newCapacity) {
                    Entry[] oldTable = table;
                    int oldCapacity = oldTable.length;
                    if (oldCapacity == MAXIMUM_CAPACITY) {
                        threshold = Integer.MAX_VALUE;
                        return;
                    }

                    Entry[] newTable = new Entry[newCapacity];
                    transfer(newTable, initHashSeedAsNeeded(newCapacity));
                    table = newTable;
                    threshold = (int)Math.min(newCapacity * loadFactor, MAXIMUM_CAPACITY + 1);
            }
#### HashMap\HashTable区别

            HashMap基于Map接口实现，元素以键值对的方式存储，并且允许使用null键和null值，因为key不允许重复，因此只能有一个键为null,
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
             }
