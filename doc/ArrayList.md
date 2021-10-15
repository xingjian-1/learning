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
                        if (s == elementData.length)
                            elementData = grow();
                        elementData[s] = e;
                        size = s + 1;
                    }
