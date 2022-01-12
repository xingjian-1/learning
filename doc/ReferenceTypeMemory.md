##### 引用类型

String类是由final关键字修饰的，所以具有不可变性。它的不可变性体现在,如下所示：

          public class App {
              public static void main(String[] args) {
                  String a = "111";
                  a = "222";
                  System.out.println(a);
              }
          }
在JVM的运行时，会单独给一块地分给String。字符串的分配和其他对象分配一样，需要消耗高昂的时间和空间的，而且字符串一般使用的非常多，JVM为了提高性能和减少内存的开销，在实例化字符串的时候进行了一些优化：使用字符串常量池，创建字符串常量时，JVM首先检查字符串常量池，如果该字符串已经存在常量池中，那么就直接返回常量池中的实例引用。如果字符串不存在常量池中，就会实例化该字符串并且将其放到常量池中。由于String字符串的不可变性我们可以十分肯定常量池中一定不存在两个相同的字符串。<br>
如上所示代码：先去JVM常量池里找，找到了就不用创建对象了，直接把对象的引用地址赋给a。找不到会重新创建一个对象，然后把对象的引用地址赋给a。同理a="222";也是先找，找不到就重新创建一个对象，然后把对象的引用地址赋给a。<br>
引用类型声明的变量是指该变量在内存中实际存储的是一个引用地址，实体在堆中。<br>
所以上面String a = “111”；表达的是变量a里保存了“111”这个对象的引用地址。变量是可以变的，不能变的是“111”。
##### final的作用
当用final修饰一个类时，表明这个类不能被继承。也就是说，如果一个类你永远不想让他被继承，就可以用final进行修饰。final类中的成员变量可以根据需要设为final，但是要注意final类中的所有成员方法都会被隐式地指定为final方法。

当final修饰的方法表示此方法已经是“最后的、最终的”含义，亦即此方法不能被重写（可以重载多个final修饰的方法）。此处需要注意的一点是：因为重写的前提是子类可以从父类中继承此方法，如果父类中final修饰的方法同时访问控制权限为private，将会导致子类中不能直接继承到此方法，因此，此时可以在子类中定义相同的方法名和参数，此时不再产生重写与final的矛盾，而是在子类中重新定义了新的方法。（注：类的private方法会隐式地被指定为final方法。）

当final修饰一个基本数据类型时，表示该基本数据类型的值一旦在初始化后便不能发生变化。如果final修饰一个引用类型时，则在对其初始化之后便不能再让其指向其他对象了，但该引用所指向的对象的内容是可以发生变化的。本质上是一回事，因为引用的值是一个地址，final要求值，即地址的值不发生变化。另外final修饰一个成员变量（属性），必须要显示初始化。这里有两种初始化方式，（1.在申明的时候给其赋值，否则必须在其类的所有构造方法中都要为其赋值）
##### String几个常用方法源码

        public class FinalDemo {
            private final String name;//1

            public FinalDemo(String name) {//2
                this.name = name;//3
            }

            public FinalDemo() {//4
            }
        }
 1处不会通过编译，要先给name初始化值才可以通过编译。
 
           public String concat(String str) {
                  int otherLen = str.length();
                  if (otherLen == 0) {
                      //啥都没有，就直接把当前字符串给你
                      return this;
                  }
                  int len = value.length;
                  char buf[] = Arrays.copyOf(value, len + otherLen);
                  str.getChars(buf, len);
                  //看到了吗？返回的居然是新的String对象
                  return new String(buf, true);
              }
              void getChars(char dst[], int dstBegin) {
                  System.arraycopy(value, 0, dst, dstBegin, value.length);
              }    
              public String replace(char oldChar, char newChar) {
                  //如果两个是一样的，那就必要替换了，所以返回this
                  if (oldChar != newChar) {
                      int len = value.length;
                      int i = -1;
                      //把当前的char数组复制给val，然后下面基于val来操作
                      char[] val = value; 
                      while (++i < len) {
                          if (val[i] == oldChar) {
                              break;
                          }
                      }
                      if (i < len) {
                          //创建一个新的char数组
                          char buf[] = new char[len];
                          for (int j = 0; j < i; j++) {
                              buf[j] = val[j];
                          }
                          while (i < len) {
                              char c = val[i];
                              buf[i] = (c == oldChar) ? newChar : c;
                              i++;
                          }
                          //创建一个新的String对象
                          return new String(buf, true);
                      }
                  }
                  return this;
              }
              public String substring(int beginIndex, int endIndex) {
                  if (beginIndex < 0) {
                      throw new StringIndexOutOfBoundsException(beginIndex);
                  }
                  if (endIndex > value.length) {
                      throw new StringIndexOutOfBoundsException(endIndex);
                  }
                  int subLen = endIndex - beginIndex;
                  if (subLen < 0) {
                      throw new StringIndexOutOfBoundsException(subLen);
                  }
                  //正常返回的都是新new出来的String对象
                  return ((beginIndex == 0) && (endIndex == value.length)) ? this
                          : new String(value, beginIndex, subLen);
              }
              public String trim() {
                  int len = value.length;
                  int st = 0;
                  char[] val = value;    /* avoid getfield opcode */
                  while ((st < len) && (val[st] <= ' ')) {
                      st++;
                  }
                  while ((st < len) && (val[len - 1] <= ' ')) {
                      len--;
                  }
                  //如果是该字符串中包含了空格，调用substring方法，否则就是啥都没干原本返回
                  //就是如果字符串里有空格，那么还是新生一个String对象返回
                  return ((st > 0) || (len < value.length)) ? substring(st, len) : this;
              }
无论是concat、replace、substring还是trim方法的操作都不是在原有的字符串上进行的，而是重新生成了一个新的字符串对象。也就是说进行这些操作后，最原始的字符串并没有被改变<br>
得出的结论：<br>
String对象一旦被创建就是固定不变的了，对String对象的任何改变都不影响到原对象，相关的任何变化性的操作都会生成新的对象。<br>
##### 示例

        public class App {
            public static void main(String[] args) {
                String a = "111";
                String a1 = "111";

                String b = new String("111");

                //对象地址是同一个
                System.out.println(a==a1);
                //对象内容是一样的
                System.out.println(a.equals(a1));
                //对象地址不一样
                System.out.println(a==b);
                //对象内容是一样的
                System.out.println(a.equals(b));
            }
        }
输出结果：true true false true <br>
结果解析：<br>
第一个输出true，a和a1两个变量保存的引用地址是同一个。<br>
第二个输出true，a和a1引用地址中内容是一样的。<br>
String a = "111"; 在JVM申请内存存放"111"对应的对象，当String a1="111";的时候，先去JVM的那块地里寻找是否存在"111"，如果存在直接把对象的引用地址给a1。此时的a和a1都保存着同一个引用地址。String b = new String("111");创建一个对象然后把对象引用地址赋给变量b。但是这里有个特殊点，那就是（“111”）,会先去JVM里找，找到了直接存放引用地址。找不到创建一个对象然后把引用地址给String的有参构造方法里。<br>
所以第三个中输出false，因为a和b所保存的对象引用是不一样的。<br>
最后一个输出true。那是因为两个变量所保存的引用地址中的内容都是“111”.
