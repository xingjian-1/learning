##### 继承关系 
在jdk11中的linkedList继承关系：<br>
public abstract class AbstractCollection<E> implements Collection<E> <br>
public abstract class AbstractList<E> extends AbstractCollection<E> implements List<E> <br>
public abstract class AbstractSequentialList<E> extends AbstractList<E> <br>
public class LinkedList<E>extends AbstractSequentialList<E>implements List<E>, Deque<E>, Cloneable, java.io.Serializable <br>
linkedList实现了List接口，list接口里面包含了所有向外暴漏的方法。另外它还实现Deque队列、Cloneable、java.io.Serializable序列化的接口,如下： <br>
  
              List list = new LinkedList();   
              LinkedList newList = new LinkedList();
                      newList .add("第一个");
                      newList .add("第二个");
                      newList .add("第三个");
                      newList .add("第四个");
                      newList .add("第二个");
                      newList .get(1);
                      newList.clone();//浅拷贝
              Deque<?> d = new LinkedList<>();
                      d.getFirst();
##### 查询

             //获取指定位置的元素接口：
             E get(int index);
             //获取指定位置元素接口实现：
             public E get(int index) {
                    checkElementIndex(index);
                    return node(index).item;
                }
            private void checkElementIndex(int index) {
                    if (!isElementIndex(index))//如果是false则抛出数组下标越界异常
                        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
                }
            //如果是第一个元素或者超过当前集合的长度返回false
            private boolean isElementIndex(int index) {
                    return index >= 0 && index < size;
                }
            //封装异常信息返回当前坐标和集合长度
            private String outOfBoundsMsg(int index) {
                    return "Index: "+index+", Size: "+size;
                }
            //LinkedList使用Node类代替了数组，根据自身存储的prev和next索引逐个遍历所有对象
            Node<E> node(int index) {//泛型标记符：E - Element表示集合中存放的元素
                    // assert isElementIndex(index);
                    if (index < (size >> 1)) {//位运算符(右移运算符)，例如：将数字size右移1位。首先把size转化为二进制，如果size为3的话那么二进制数就是：0000 0000 0000 0000 0000 0000                         0000 0011，然后把低位的最后一个数字移出，高位补零，结果为：0000 0000 0000 0000 0000 0000 0000 0001，转换为十进制数是1（右移一位相当于除2，右移n位相当于除以2的n次方）
                        Node<E> x = first;
                        for (int i = 0; i < index; i++)
                            x = x.next;
                        return x;
                    } else {
                        Node<E> x = last;
                        for (int i = size - 1; i > index; i--)
                            x = x.prev;
                        return x;
                    }
                }
##### 添加

                boolean add(E e);
                public boolean add(E e) {
                        linkLast(e);
                        return true;
                    }
                /**
                     * Links e as last element.
                     */
                    void linkLast(E e) {
                        final Node<E> l = last;
                        final Node<E> newNode = new Node<>(l, e, null);//LinkedList每次添加元素都要创建Node对象
                        last = newNode;
                        if (l == null)
                            first = newNode;
                        else
                            l.next = newNode;
                        size++;
                        modCount++;
                    }
