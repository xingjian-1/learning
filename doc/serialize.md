##### 什么是序列化和反序列化
序列化(Serialization)：一个对象转化为二进制流的过程。反序列化(Deserialization)：对象的二进制流重新读取转换成对象的过程。

##### 什么时候用序列化
简单的写一个 hello world 程序，用不到序列化和反序列化。写一个排序算法也用不到序列化和反序列化。但是当你想要将一个对象进行持久化写入文件，或者你想将一个对象从一个网络地址通过网络协议发送到另一个网络地址时，这时候就需要考虑序列化和反序列化了。另外如果对一个对象实例进行深度拷贝，也可以通过序列化和反序列化的方式进行。

##### 怎么实现序列化
实现了 Serializable 或 Externalizable 接口的类的对象才能被序列化，否则抛出异常。序列化的时候系统会把当前类的 serialVersionUID 写入序列化的二进制文件中，当反序列化的时候系统会检测文件中的 serialVersionUID 是否和当前类的 serialVersionUID 一致，如果一致就说明序列化的类的版本和当前类的版本是相同的，这个时候可以成功反序列化；否则说明当前类和反序列化的类相比发生了某些变化，比如成员变量的数量、类型发生了变化，这个时候是无法正常反序列化的。<br>
序列化和反序列化的过程分三种情况：<br>
<br>
情况 1：若类仅仅实现了 Serializable 接口<br>
ObjectOutputStream 采用默认的序列化方式，对对象的非 transient 的实例变量进行序列化。ObjcetInputStream 采用默认的反序列化方式，对对象的非 transient 的实例变量进行反序列化。<br>
<br>
情况 2：若类不仅实现了 Serializable 接口，并且还定义了 readObject(ObjectInputStream in) 和 writeObject(ObjectOutputSteam out)。<br>
ObjectOutputStream 调用对象的 writeObject(ObjectOutputStream out) 的方法进行序列化。ObjectInputStream 会调用对象的 readObject(ObjectInputStream in) 的方法进行反序列化。<br>
<br>
情况 3：若类实现了 Externalnalizable 接口，且类必须实现 readExternal(ObjectInput in) 和 writeExternal(ObjectOutput out) 方法。<br>
ObjectOutputStream 调用对象的 writeExternal(ObjectOutput out)) 的方法进行序列化。ObjectInputStream 会调用对象的 readExternal(ObjectInput in) 的方法进行反序列化。<br>

jdk 底层 ArrayList 的序列化和反序列化：

          // 实现了Serializable接口，可以被序列化
          public class ArrayList<E> extends AbstractList<E>implements List<E>, RandomAccess, Cloneable, java.io.Serializable{
              private static final long serialVersionUID = 8683452581122892189L;

              /**
               * The array buffer into which the elements of the ArrayList are stored.
               * The capacity of the ArrayList is the length of this array buffer.
               */
              // 实际元素被transient修饰,默认不会进行序列化
              private transient Object[] elementData;

              .....

              /**
               * Save the state of the <tt>ArrayList</tt> instance to a stream (that
               * is, serialize it).
               *
               * @serialData The length of the array backing the <tt>ArrayList</tt>
               *             instance is emitted (int), followed by all of its elements
               *             (each an <tt>Object</tt>) in the proper order.
               */
              private void writeObject(java.io.ObjectOutputStream s)throws java.io.IOException{
                  // Write out element count, and any hidden stuff
                  int expectedModCount = modCount;
                  s.defaultWriteObject();
                  // Write out array length
                  s.writeInt(elementData.length);

                  // Write out all elements in the proper order.
                  for (int i=0; i<size; i++){
                          s.writeObject(elementData[i]);
                      }
                  if (modCount != expectedModCount) {
                          throw new ConcurrentModificationException();
                      }
               }

              /**
               * Reconstitute the <tt>ArrayList</tt> instance from a stream (that is,
               * deserialize it).
               */
              private void readObject(java.io.ObjectInputStream s)throws java.io.IOException, ClassNotFoundException {
                  // Read in size, and any hidden stuff
                  s.defaultReadObject();
                  // Read in array length and allocate array
                  int arrayLength = s.readInt();
                  Object[] a = elementData = new Object[arrayLength];
                  // Read in all elements in the proper order.
                  for (int i=0; i<size; i++){
                       a[i] = s.readObject();
                      }
                  }
              }
初看之下ArrayList不能被序列化。但其重写了writeObject和readObject方法, 在方法的内部实现了对具体存储对象的序列化与反序列化。

##### 总结序列化反序列化的注意点
父类实现了序列化，则子类自动实现了序列化，即子类不需要实现 Serializable 接口。<br>
当父类没有实现序列化，而子类需要实现时，子类需要显式实现 Serializable 接口，并且父类中需要有无参的构造函数。<br>
序列化只对对象的属性进行保存，而不会保存其方法。<br>
当类中的实例变量引用了其他对象，那么在对该类进行序列化时，引用的对象也会被序列化（需要这个引用的对象也实现 Serializable 接口，否则会出现 java.io.NotSerializableException）。<br>
静态成员变量属于类不属于对象，所以不参与序列化过程<br>
用 transient 关键字标记的成员变量不参与序列化过程<br>
