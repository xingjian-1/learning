#### ArrayList扩容机制

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
#### 泛型
