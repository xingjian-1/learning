##### 继承关系
public abstract class AbstractCollection<E> implements Collection<E> <br>
public abstract class AbstractList<E> extends AbstractCollection<E> implements List<E> <br>
public class ArrayList<E> extends AbstractList<E>implements List<E>, RandomAccess, Cloneable, java.io.Serializable <br>
##### 查询
  
                 E get(int index);
                 public E get(int index) {
                      Objects.checkIndex(index, size);//校验长度
                      return elementData(index);
                  }
                  @ForceInline
                  public static int checkIndex(int index, int length) {
                      return Preconditions.checkIndex(index, length, null);
                  }
                  @HotSpotIntrinsicCandidate
                  public static <X extends RuntimeException>
                  int checkIndex(int index, int length,BiFunction<String, List<Integer>, X> oobef) {
                      if (index < 0 || index >= length)
                          throw outOfBoundsCheckIndex(oobef, index, length);
                      return index;
                  }
                  @SuppressWarnings("unchecked")
                  E elementData(int index) {
                      return (E) elementData[index];
                  }
                 //transient关键字：transient一般在实现了Serializable接口的类中使用，被transient修饰的变量不参与序列化和反序列化。
                 transient Object[] elementData;
##### 添加
  
                boolean add(E e);
                public boolean add(E e) {
                        modCount++;
                        add(e, elementData, size);
                        return true;
                    }
                 private void add(E e, Object[] elementData, int s) {
                        if (s == elementData.length){
                            elementData = grow();
                          }
                        elementData[s] = e;
                        size = s + 1;
                    }
                private Object[] grow() {
                      return grow(size + 1);
                  }
                private Object[] grow(int minCapacity) {
                      return elementData = Arrays.copyOf(elementData,newCapacity(minCapacity));
                  }
                private int newCapacity(int minCapacity) {
                      // overflow-conscious code
                      int oldCapacity = elementData.length;
                      int newCapacity = oldCapacity + (oldCapacity >> 1);
                      if (newCapacity - minCapacity <= 0) {
                          if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
                              return Math.max(DEFAULT_CAPACITY, minCapacity);
                          if (minCapacity < 0) // overflow
                              throw new OutOfMemoryError();
                          return minCapacity;
                      }
                      return (newCapacity - MAX_ARRAY_SIZE <= 0)
                          ? newCapacity
                          : hugeCapacity(minCapacity);
                  }
                private static int hugeCapacity(int minCapacity) {
                    if (minCapacity < 0) // overflow
                        throw new OutOfMemoryError();
                    return (minCapacity > MAX_ARRAY_SIZE)
                        ? Integer.MAX_VALUE
                        : MAX_ARRAY_SIZE;
                }
##### 线程不安全的情况
在多个线程进行add操作时可能会导致elementData数组越界。比如：列表大小为9，即size=9，线程A开始进入add方法，这时它获取到size的值为9，调用ensureCapacityInternal方法进行容量判断。线程B此时也进入add方法，它获取到size的值也为9，也开始调用ensureCapacityInternal方法。线程A发现需求大小为10，而elementData的大小就为10，可以容纳。于是它不再扩容，返回。线程B也发现需求大小为10，也可以容纳，返回。线程A开始进行设置值操作， elementData[size++] = e 操作。此时size变为10。线程B也开始进行设置值操作，它尝试设置elementData[10] = e，而elementData没有进行过扩容，它的下标最大为9。于是此时会报出一个数组越界的异常ArrayIndexOutOfBoundsException。<br>

 elementData[size++] = e 设置值的操作同样会导致线程不安全。从这儿可以看出，这步操作也不是一个原子操作，它由如下两步操作构成：

                elementData[size] = e;
                size = size + 1;
在单线程执行这两条代码时没有任何问题，但是当多线程环境下执行时，可能就会发生一个线程的值覆盖另一个线程添加的值，具体逻辑：列表大小为0，即size=0线程A开始添加一个元素，值为A。此时它执行第一条操作，将A放在了elementData下标为0的位置上。接着线程B刚好也要开始添加一个值为B的元素，且走到了第一步操作。此时线程B获取到size的值依然为0，于是它将B也放在了elementData下标为0的位置上。线程A开始将size的值增加为1线程B开始将size的值增加为2这样线程AB执行完毕后，理想中情况为size为2，elementData下标0的位置为A，下标1的位置为B。而实际情况变成了size为2，elementData下标为0的位置变成了B，下标1的位置上什么都没有。并且后续除非使用set方法修改此位置的值，否则将一直为null，因为size为2，添加元素时会从下标为2的位置上开始。示例代码如下：
  
        public static void main(String[] args) throws InterruptedException {
              final List<Integer> list = new ArrayList<Integer>();
              // 线程A将1-1000添加到列表
              new Thread(new Runnable() {
                  @Override
                  public void run() {
                      for (int i = 1; i < 1000; i++) {
                          list.add(i);
                          try {
                              Thread.sleep(1);
                          } catch (InterruptedException e) {
                              e.printStackTrace();
                          }
                      }

                  }
              }).start();

              // 线程B将1001-2000添加到列表
              new Thread(new Runnable() {
                  @Override
                  public void run() {
                      for (int i = 1001; i < 2000; i++) {
                          list.add(i);
                          try {
                              Thread.sleep(1); 
                          } catch (InterruptedException e) {
                              e.printStackTrace();
                          }
                      }
                  }
              }).start();
              Thread.sleep(1000);
              // 打印所有结果
              for (int i = 0; i < list.size(); i++) {
                  System.out.println("第" + (i + 1) + "个元素为：" + list.get(i));
              }
          }
##### 线程安全处理
1.最常用的方法是通过Collections.synchronizedList() 方法将ArrayList转换成线程安全的容器后再使用。

            List<Object> list =Collections.synchronizedList(new ArrayList<Object>);
2.为list.add()方法加锁
  
            synchronized(ar) {
              ar.add("2");
            }
3.CopyOnWriteArrayList
使用线程安全的 CopyOnWriteArrayList代替线程不安全的 ArrayList。

            List<Object> list1 = new CopyOnWriteArrayList<Object>();
4.使用ThreadLocal
使用ThreadLocal变量确保线程封闭性(由于使用ThreadLocal封装变量，相当于把变量丢进执行线程中去，每new一个新的线程，变量也会new一次，会占用内存，但其执行完毕就销毁的机制使得ThreadLocal变成比较好的优化解决这类问题的方案)。

            ThreadLocal<List<Object>> threadList = new ThreadLocal<List<Object>>() {
                @Override
                protected List<Object> initialValue() {
                      return new ArrayList<Object>();
                }
            }
