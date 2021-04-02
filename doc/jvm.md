## `JDK和JRE有区别`
###### JRE(Java Runtime Environment， Java运行环境)是Java平台，所有的程序都要在JRE下才能够运行。包括JVM和Java核心类库和支持文件。JDK(Java Development Kit，Java开发工具包)是用来编译、调试Java程序的开发工具包。包括Java工具(javac/java/jdb等)和Java基础的类库(java API )。JVM(Java Virtual Machine， Java虚拟机)是JRE的一部分。JVM主要工作是解释自己的指令集(即字节码)并映射到本地的CPU指令集和OS的系统调用。Java语言是跨平台运行的，不同的操作系统会有不同的JVM映射规则，使之与操作系统无关，完成跨平台性。
## `JVM主要组成部分及其作用`
###### 类加载器-ClassLoader、运行时数据区-Runtime Data Area、执行引擎-Execution Engine、本地库接口（Native Interface）组件的作用： 首先通过类加载器（ClassLoader）会把 Java 代码转换成字节码，运行时数据区（Runtime Data Area）再把字节码加载到内存中，而字节码文件只是 JVM 的一套指令集规范，并不能直接交给底层操作系统去执行，因此需要特定的命令解析器执行引擎（Execution Engine），将字节码翻译成底层系统指令，再交由 CPU 去执行，而这个过程中需要调用其他语言的本地库接口（Native Interface）来实现整个程序的功能

## `运行时数据区`
###### 程序计数器（Program Counter Register）：当前线程所执行的字节码的行号指示器，字节码解析器的工作是通过改变这个计数器的值，来选取下一条需要执行的字节码指令，分支、循环、跳转、异常处理、线程恢复等基础功能，都需要依赖这个计数器来完成；Java 虚拟机栈（Java Virtual Machine Stacks）：用于存储局部变量表、操作数栈、动态链接、方法出口等信息；本地方法栈（Native Method Stack）：与虚拟机栈的作用是一样的，只不过虚拟机栈是服务 Java 方法的，而本地方法栈是为虚拟机调用 Native 方法服务的；Java 堆（Java Heap）：Java 虚拟机中内存最大的一块，是被所有线程共享的，几乎所有的对象实例都在这里分配内存；方法区（Methed Area）：用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译后的代码等数据。

## `堆和栈的区别`
###### 功能方面：堆是用来存放对象的，栈是用来执行程序的。共享性：堆是线程共享的，栈是线程私有的。空间大小：堆大小远远大于栈。

## `队列和栈的区别`
###### 队列和栈都是被用来预存储数据的。队列允许先进先出检索元素，但也有例外的情况，Deque 接口允许从两端检索元素。栈和队列很相似，但它运行对元素进行后进先出进行检索。

## `什么是双亲委派模型`
###### 在介绍双亲委派模型之前先说下类加载器。对于任意一个类，都需要由加载它的类加载器和这个类本身一同确立在 JVM 中的唯一性，每一个类加载器，都有一个独立的类名称空间。类加载器就是根据指定全限定名称将 class 文件加载到 JVM 内存，然后再转化为 class 对象。
###### 类加载器分类
* 启动类加载器（Bootstrap ClassLoader），是虚拟机自身的一部分，用来加载Java_HOME/lib/目录中的，
  或者被 -Xbootclasspath 参数所指定的路径中并且被虚拟机识别的类库；
* 其他类加载器：
* 扩展类加载器（Extension ClassLoader）：负责加载\lib\ext目录或Java. ext. dirs系统变量指定的路径中的所有类库；
* 应用程序类加载器（Application ClassLoader）。负责加载用户类路径（classpath）上的指定类库，我们可以直接使用这个类加载器。一般情况，
  如果我们没有自定义类加载器默认就是用这个加载器。
  双亲委派模型：如果一个类加载器收到了类加载的请求，它首先不会自己去加载这个类，而是把这个请求委派给父类加载器去完成，每一层的类加载器都是如此，
  这样所有的加载请求都会被传送到顶层的启动类加载器中，只有当父加载无法完成加载请求（它的搜索范围中没找到所需的类）时，子加载器才会尝试去加载类
  
## `类加步骤`
###### 加载、校验、准备、初始化、解析、使用、销毁
  
## `怎么判断对象可以被回收`
###### 一般有两种方法来判断：
* 引用计数器：为每个对象创建一个引用计数，有对象引用时计数器 +1，引用被释放时计数 -1，当计数器为 0 时就可以被回收。它有一个缺点不能解决循环引用的问题；
* 可达性分析：从 GC Roots 开始向下搜索，搜索所走过的路径称为引用链。当一个对象到 GC Roots 没有任何引用链相连时，则证明此对象是可以被回收的。

## `JVM有哪些垃圾回收算法`
* 标记-清除算法：标记无用对象，然后进行清除回收。缺点：效率不高，无法清除垃圾碎片。
* 标记-整理算法：标记无用对象，让所有存活的对象都向一端移动，然后直接清除掉端边界以外的内存。
* 复制算法：按照容量划分二个大小相等的内存区域，当一块用完的时候将活着的对象复制到另一块上，然后再把已使用的内存空间一次清理掉。
  缺点：内存使用率不高，只有原来的一半。
* 分代算法：根据对象存活周期的不同将内存划分为几块，一般是新生代和老年代，新生代基本采用复制算法，老年代采用标记整理算法。

## `jvm有哪些垃圾回收器`
* Serial：最早的单线程串行垃圾回收器。
* Serial Old：Serial 垃圾回收器的老年版本，同样也是单线程的，可以作为 CMS 垃圾回收器的备选预案。
* ParNew：是 Serial 的多线程版本。
* Parallel 和 ParNew 收集器类似是多线程的，但 Parallel 是吞吐量优先的收集器，可以牺牲等待时间换取系统的吞吐量。
* Parallel Old 是 Parallel 老生代版本，Parallel 使用的是复制的内存回收算法，Parallel Old 使用的是标记-整理的内存回收算法。
* CMS：一种以获得最短停顿时间为目标的收集器，非常适用 B/S 系统。
* G1：一种兼顾吞吐量和停顿时间的 GC 实现，是 JDK 9 以后的默认 GC 选项。

## `详细介绍一下 CMS 垃圾回收器`
######  CMS 是英文 Concurrent Mark-Sweep 的简称，是以牺牲吞吐量为代价来获得最短回收停顿时间的垃圾回收器。对于要求服务器响应速度的应用上，这种垃圾回收器非常适合。在启动 JVM 的参数加上“-XX:+UseConcMarkSweepGC”来指定使用 CMS 垃圾回收器。CMS 使用的是标记-清除的算法实现的，所以在 gc 的时候回产生大量的内存碎片，当剩余内存不能满足程序运行要求时，系统将会出现 Concurrent Mode Failure，临时 CMS 会采用 Serial Old 回收器进行垃圾清除，此时的性能将会被降低

## `新生代垃圾回收器和老生代垃圾回收器都有哪些？有什么区别`
* 新生代回收器：Serial、ParNew、Parallel Scavenge
* 老年代回收器：Serial Old、Parallel Old、CMS
* 整堆回收器：G1
* 新生代垃圾回收器一般采用的是复制算法，复制算法的优点是效率高，缺点是内存利用率低；老年代回收器一般采用的是标记-整理的算法进行垃圾回收。
  
## `简述分代垃圾回收器是怎么工作的`
* 分代回收器有两个分区：老生代和新生代，新生代默认的空间占比总空间的 1/3，老生代的默认占比是 2/3。
* 新生代使用的是复制算法，新生代里有 3 个分区：Eden、To Survivor、From Survivor，它们的默认占比是 8:1:1，它的执行流程如下：
* 把 Eden + From Survivor 存活的对象放入 To Survivor 区；
* 清空 Eden 和 From Survivor 分区；
* From Survivor 和 To Survivor 分区交换，From Survivor 变 To Survivor，To Survivor 变 From Survivor。
* 每次在 From Survivor 到 To Survivor 移动时都存活的对象，年龄就 +1，当年龄到达 15（默认配置是 15）时，升级为老生代。大对象也会直接进入老生代。
* 老生代当空间占用到达某个值之后就会触发全局垃圾收回，一般使用标记整理的执行算法。以上这些循环往复就构成了整个分代垃圾回收的整体执行流程。
  
  ## `jvm调优工具`
###### JDK 自带了很多监控工具，都位于 JDK 的 bin 目录下，其中最常用的是 jconsole 和 jvisualvm 这两款视图监控工具。
* jconsole：用于对 JVM 中的内存、线程和类等进行监控；
* jvisualvm：JDK 自带的全能分析工具，可以分析：内存快照、线程快照、程序死锁、监控内存的变化、gc 变化等。

## `JVM调优的参数有哪些`
* -Xms2g：初始化推大小为 2g；
* -Xmx2g：堆最大内存为 2g；
* -XX:NewRatio=4：设置年轻的和老年代的内存比例为 1:4；
* -XX:SurvivorRatio=8：设置新生代 Eden 和 Survivor 比例为 8:2；
* –XX:+UseParNewGC：指定使用 ParNew + Serial Old 垃圾回收器组合；
* -XX:+UseParallelOldGC：指定使用 ParNew + ParNew Old 垃圾回收器组合；
* -XX:+UseConcMarkSweepGC：指定使用 CMS + Serial Old 垃圾回收器组合；
* -XX:+PrintGC：开启打印 gc 信息；
* -XX:+PrintGCDetails：打印 gc 详细信息。

###### ==和equal的区别
###### 基本类型：比较的是值是否相同；引用类型：比较的是引用是否相同；
        代码示例：
                String x = "string";
                String y = "string";
                String z = new String("string");
                System.out.println(x==y); // true
                System.out.println(x==z); // false
                System.out.println(x.equals(y)); // true
                System.out.println(x.equals(z)); // true
###### 代码解读：因为 x和y指向的是同一个引用，所以==也是 true，而new String()方法则重写开辟了内存空间，所以==结果为 false，而equals 比较的一直是值，所以结果都为 true。equals 解读equals 本质上就是 ==，只不过 String 和 Integer 等重写了 equals 方法，把它变成了值比较。看下面的代码就明白了。首先来看默认情况下 equals 比较一个有相同值的对象，代码如下：
              class Cat {
                  public Cat(String name) {
                      this.name = name;
                  }
                  private String name;
                  public String getName() {
                      return name;
                  }
                  public void setName(String name) {
                      this.name = name;
                  }
              }
              Cat c1 = new Cat("王磊");
              Cat c2 = new Cat("王磊");
              System.out.println(c1.equals(c2)); // false
              输出结果出乎我们的意料，竟然是 false？这是怎么回事，看了 equals 源码就知道了，源码如下：
              public boolean equals(Object obj) {
                      return (this == obj);
              }
              原来 equals 本质上就是 ==。
              那问题来了，两个相同值的 String 对象，为什么返回的是 true？代码如下：
              String s1 = new String("老王");
              String s2 = new String("老王");
              System.out.println(s1.equals(s2)); // true
              同样的，当我们进入 String 的 equals 方法，找到了答案，代码如下：
              public boolean equals(Object anObject) {
                  if (this == anObject) {
                      return true;
                  }
                  if (anObject instanceof String) {
                      String anotherString = (String)anObject;
                      int n = value.length;
                      if (n == anotherString.value.length) {
                          char v1[] = value;
                          char v2[] = anotherString.value;
                          int i = 0;
                          while (n-- != 0) {
                              if (v1[i] != v2[i])
                                  return false;
                              i++;
                          }
                          return true;
                      }
                  }
                  return false;
              }
###### 原来是String重写了Object 的equals方法，把引用比较改成了值比较。总结 ：== 对于基本类型来说是值比较，对于引用类型来说是比较的是引用；而 equals 默认情况下是引用比较，只是很多类重新了 equals 方法，比如 String、Integer 等把它变成了值比较，所以一般情况下 equals 比较的是值是否相等。
