
#### I/O流
JDK开发人编写大量的类只为了能够创建良好的工具包，IO类设计出来，为了解决IO相关操作，最常见的I/O操作就是网络、磁盘等。
它是一种数据的流从源头流到目的地。比如文件拷贝，输入流和输出流都包括了。输入流从文件中读取数据存储到进程(process)中，输出流从进程中读取数据然后写入到目标文件。
##### Java中有几种类型的流？
* 按照流的方向：输入流（inputStream）输出流（outputStream）。
* 按照实现功能分：字节流、字符流。字节流继承于InputStream和OutputStream。字符流继承于InputStreamReader和OutputStreamWriter
字节流在JDK.0中就被引进了，用于操作包含ASCII字符的文件。JAVA也支持其他的字符如Unicode，为了读取包含Unicode字符的文件，JAVA语言设计者在JDK1.1中引入了字符流。ASCII作为Unicode的子集，对于英语字符的文件，可以可以使用字节流也可以使用字符流。
字符流是由 Java 虚拟机将字节转换得到的，问题就出在这个过程还算是非常耗时，并且，如果我们不知道编码类型就很容易出现乱码问题。所以， I/O 流就干脆提供了一个直接操作字符的接口，方便我们平时对字符进行流操作。如果音频文件、图片等媒体文件用字节流比较好，如果涉及到字符的话使用字符流比较好。
##### FileInputStream和FileOutputStream是什么？
这是在拷贝文件操作的时候，经常用到的两个类。在处理小文件的时候，它们性能表现还不错，在大文件的时候，最好使用BufferedInputStream (或 BufferedReader) 和 BufferedOutputStream (或 BufferedWriter)
##### Files的常用方法都有哪些？
Files. size()：查看文件个数。
Files. read()：读取文件。
Files. write()：写入文件。
Files. exists()：检测文件路径是否存在。
Files. createFile()：创建文件。
Files. createDirectory()：创建文件夹。
Files. delete()：删除一个文件或目录。
Files. copy()：复制文件。
Files. move()：移动文件。
##### 什么是 java 序列化，如何实现 java 序列化？
* 序列化：
是一种用来处理对象流的机制，所谓对象流也就是将对象的内容进行流化。可以对流化后的对象进行读写操作，也可将流化后的对象传输于网络之间。序列化是为了解决在对对象流进行读写操作时所引发的问题。<br>
序列化的实现：
将需要被序列化的类实现Serializable接口 ，该接口没有需要 实现的方法，implements Serializable只是为了标注该对象是可被序列化的，然后使用一个输出流(如：FileOutputStream)来构造
一个 ObjectOutputStream(对象流)对象，接着，使用 ObjectOutputStream 对象的 writeObject(Object obj)方法就可以将参数为 obj的对象写出(即保存其状态)，要恢复的话则用输入流。
##### 如何将一个 java 对象序列化到文件里?
在 java 中能够被序列化的类必须先实现Serializable接口，该接口没有任何抽象方法只是起到一个标记作用。
##### 如何实现对象克隆？
两种方式:
实现 Cloneable 接口并重写 Object 类中的 clone()方法；
实现 Serializable接口，通过对象的序列化和反序列化实现克隆，可以实现真正的深度克隆。
注意：基于序列化和反序列化实现的克隆不仅仅是深度克隆，更重要的是通过泛型限定，可以检查出要克隆的对象是否支持序列化，这项检查是编译器完成的，不是在运行时抛出异常，这种是方案明显优于使用 Object 类的 clone
方法克隆对象。让问题在编译的时候暴露出来总是好过把问题留到运行时。

###### BIO、NIO、AIO区别
###### BIO：Block IO 同步阻塞式 IO，就是我们平常使用的传统 IO，它的特点是模式简单使用方便，并发处理能力低。NIO：Non IO 同步非阻塞 IO，是传统 IO 的升级，客户端和服务器端通过 Channel（通道）通讯，实现了多路复用。AIO：Asynchronous IO 是 NIO 的升级，也叫 NIO2，实现了异步非堵塞 IO ，异步 IO 的操作基于事件和回调机制。
###### Files的常用方法
Files. exists()：检测文件路径是否存在。
Files. createFile()：创建文件。
Files. createDirectory()：创建文件夹。
Files. delete()：删除一个文件或目录。
Files. copy()：复制文件。
Files. move()：移动文件。
Files. size()：查看文件个数。
Files. read()：读取文件。
Files. write()：写入文件。
