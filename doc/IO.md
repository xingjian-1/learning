#### IO基本概念
Java对数据的操作是通过流的方式，IO流用来处理设备之间的数据传输，上传文件和下载文件，Java用于操作流的对象都在IO包中。
#### IO流的分类
* 按照流的方向：输入流（inputStream）输出流（outputStream）。
* 按照实现功能分：字节流、字符流。字节流继承于InputStream和OutputStream。字符流继承于InputStreamReader和OutputStreamWriter
字节流在JDK1.0中就被引进了，用于操作包含ASCII字符的文件。JAVA也支持其他的字符如Unicode，为了读取包含Unicode字符的文件，JAVA语言设计者在JDK1.1中引入了字符流。ASCII作为Unicode的子集，对于英语字符的文件，可以可以使用字节流也可以使用字符流。
字符流是由 Java 虚拟机将字节转换得到的，问题就出在这个过程还算是非常耗时，并且，如果我们不知道编码类型就很容易出现乱码问题。所以， I/O 流就干脆提供了一个直接操作字符的接口，方便我们平时对字符进行流操作。如果音频文件、图片等媒体文件用字节流比较好，如果涉及到字符的话使用字符流比较好。
#### 字节流
字节流基类.InputStream
* InputStream：字节输入流基类，抽象类是表示字节输入流的所有类的超类。

          常用方法：
          // 从输入流中读取数据的下一个字节
          abstract int read()
          // 从输入流中读取一定数量的字节，并将其存储在缓冲区数组 b中
          int read(byte[] b)
          // 将输入流中最多 len 个数据字节读入 byte 数组
          int read(byte[] b, int off, int len)

          // 跳过和丢弃此输入流中数据的 n个字节
          long skip(long n)
          // 关闭此输入流并释放与该流关联的所有系统资源
          void close()
* OutputStream：字节输出流基类，抽象类是表示输出字节流的所有类的超类。

          常用方法：
          // 将 b.length 个字节从指定的 byte 数组写入此输出流
          void write(byte[] b)
          // 将指定 byte 数组中从偏移量 off 开始的 len 个字节写入此输出流
          void write(byte[] b, int off, int len)
          // 将指定的字节写入此输出流
          abstract void write(int b)

          // 关闭此输出流并释放与此流有关的所有系统资源
          void close()

          // 刷新此输出流并强制写出所有缓冲的输出字节
          void flush()
字节文件操作流
* FileInputStream：字节文件输入流，从文件系统中的某个文件中获得输入字节，用于读取诸如图像数据之类的原始字节流。

          构造方法：
          // 通过打开一个到实际文件的连接来创建一个FileInputStream，该文件通过文件系统中的File对象file指定
          FileInputStream(File file)
          // 通过打开一个到实际文件的连接来创建一个FileInputStream，该文件通过文件系统中的路径name指定
          FileInputStream(String name)

          常用方法：覆盖和重写了父类的的常用方法。
          // 读取f盘下该文件f://hell/test.txt
          //构造方法1
          InputStream inputStream = new FileInputStream(new File("f://hello//test.txt"));
          int i = 0;
          //一次读取一个字节
          while ((i = inputStream.read()) != -1) {

              // System.out.print(i + " ");// 65 66 67 68
              //为什么会输出65 66 67 68？因为字符在底层存储的时候就是存储的数值。即字符对应的ASCII码。
              System.out.print((char) i + " ");// A B C D
          }
          //关闭IO流
          inputStream.close();
          // 读取f盘下该文件f://hell/test.txt
          //构造方法2
          InputStream inputStream2 = new FileInputStream("f://hello/test.txt");
          // 字节数组
          byte[] b = new byte[2];
          int i2 = 0;
          //  一次读取一个字节数组
          while ((i2 = inputStream2.read(b)) != -1) {
              System.out.print(new String(b, 0, i2) + " ");// AB CD
          }
          //关闭IO流
          inputStream2.close();
          注： 一次读取一个字节数组，提高了操作效率,IO流使用完毕一定要关闭。

* FileOutputStream：字节文件输出流是用于将数据写入到File，从程序中写入到其他位置。

          构造方法：
          // 创建一个向指定File对象表示的文件中写入数据的文件输出流
          FileOutputStream(File file)
          // 创建一个向指定File对象表示的文件中写入数据的文件输出流
          FileOutputStream(File file, boolean append)
          // 创建一个向具有指定名称的文件中写入数据的输出文件流
          FileOutputStream(String name)
          // 创建一个向具有指定name的文件中写入数据的输出文件流
          FileOutputStream(String name, boolean append)

          常用方法：覆盖和重写了父类的的常用方法。
          OutputStream outputStream = new FileOutputStream(new File("test.txt"));
          // 写出数据
          outputStream.write("ABCD".getBytes());
          // 关闭IO流
          outputStream.close();

          // 内容追加写入
          OutputStream outputStream2 = new FileOutputStream("test.txt", true);
          // 输出换行符
          outputStream2.write("\r\n".getBytes());
          // 输出追加内容
          outputStream2.write("hello".getBytes());
          // 关闭IO流
          outputStream2.close();
          注；输出的目的地文件不存在，则会自动创建，不指定盘符的话，默认创建在项目目录下;输出换行符时一定要写\r\n不能只写\n,因为不同文本编辑器对换行符的识别存在差异性。

字节缓冲流（高效流）
* BufferedInputStream：字节缓冲输入流，提高了读取效率。

           构造方法：
           // 创建一个 BufferedInputStream并保存其参数，即输入流in，以便将来使用。
           BufferedInputStream(InputStream in)
           // 创建具有指定缓冲区大小的 BufferedInputStream并保存其参数，即输入流in以便将来使用
           BufferedInputStream(InputStream in, int size)
              InputStream in = new FileInputStream("test.txt");
              // 字节缓存流
              BufferedInputStream bis = new BufferedInputStream(in);
              byte[] bs = new byte[20];
              int len = 0;
              while ((len = bis.read(bs)) != -1) {

                  System.out.print(new String(bs, 0, len));
                  // ABCD
                  // hello
              }
              // 关闭流
              bis.close();
* BufferedOutputStream：字节缓冲输出流，提高了写出效率。

             构造方法：
             // 创建一个新的缓冲输出流，以将数据写入指定的底层输出流
             BufferedOutputStream(OutputStream out)
             // 创建一个新的缓冲输出流，以将具有指定缓冲区大小的数据写入指定的底层输出流
             BufferedOutputStream(OutputStream out, int size)

             常用方法：
             // 将指定 byte 数组中从偏移量 off 开始的 len 个字节写入此缓冲的输出流
             void write(byte[] b, int off, int len)
             // 将指定的字节写入此缓冲的输出流
             void write(int b)
             // 刷新此缓冲的输出流
             void flush()
                BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("test.txt", true));
                // 输出换行符
                bos.write("\r\n".getBytes());
                // 输出内容
                bos.write("Hello Android".getBytes());
                // 刷新此缓冲的输出流
                bos.flush();
                // 关闭流
                bos.close();
                
 #### 字符流
字符流基类
* Reader:读取字符流的抽象类.

              常用方法：
              // 读取单个字符
              int read()
              // 将字符读入数组
              int read(char[] cbuf)
              // 将字符读入数组的某一部分
              abstract int read(char[] cbuf, int off, int len)
              // 跳过字符
              long skip(long n)

              // 关闭该流并释放与之关联的所有资源
              abstract void close()
* Writer:写入字符流的抽象类.

              常用方法：
              // 写入字符数组
               void write(char[] cbuf)
              // 写入字符数组的某一部分
              abstract void write(char[] cbuf, int off, int len)
              // 写入单个字符
              void write(int c)
              // 写入字符串
              void write(String str)
              // 写入字符串的某一部分
              void write(String str, int off, int len)

              // 将指定字符添加到此 writer
              Writer append(char c)
              // 将指定字符序列添加到此 writer
              Writer append(CharSequence csq)
              // 将指定字符序列的子序列添加到此 writer.Appendable
              Writer append(CharSequence csq, int start, int end)

              // 关闭此流，但要先刷新它
              abstract void close()
              // 刷新该流的缓冲
              abstract void flush()
字符转换流
* InputStreamReader：字节流转字符流，它使用的字符集可以由名称指定或显式给定，否则将接受平台默认的字符集。

              构造方法：
              // 创建一个使用默认字符集的 InputStreamReader
              InputStreamReader(InputStream in)
              // 创建使用给定字符集的 InputStreamReader
              InputStreamReader(InputStream in, Charset cs)
              // 创建使用给定字符集解码器的 InputStreamReader
              InputStreamReader(InputStream in, CharsetDecoder dec)
              // 创建使用指定字符集的 InputStreamReader
              InputStreamReader(InputStream in, String charsetName)
              特有方法：
              //返回此流使用的字符编码的名称 
              String getEncoding() 
                  //使用默认编码        
                  InputStreamReader reader = new InputStreamReader(new FileInputStream("test.txt"));
                  int len;
                  while ((len = reader.read()) != -1) {
                      System.out.print((char) len);//爱生活，爱Android

                  }
                  reader.close();

                   //指定编码 
                  InputStreamReader reader = new InputStreamReader(new FileInputStream("test.txt"),"utf-8");
                  int len;
                  while ((len = reader.read()) != -1) {
                      System.out.print((char) len);//????????Android
                  }
                  reader.close();
                注：Eclipse默认使用GBK编码,test.txt文件所以是GBK编码，当指定utf-8编码时所以会乱码。

* OutputStreamWriter：字节流转字符流。

            构造方法：
            // 创建使用默认字符编码的 OutputStreamWriter
            OutputStreamWriter(OutputStream out)
            // 创建使用给定字符集的 OutputStreamWriter
            OutputStreamWriter(OutputStream out, Charset cs)
            // 创建使用给定字符集编码器的 OutputStreamWriter
            OutputStreamWriter(OutputStream out, CharsetEncoder enc)
            // 创建使用指定字符集的 OutputStreamWriter
            OutputStreamWriter(OutputStream out, String charsetName)
          特有方法：
            //返回此流使用的字符编码的名称 
            String getEncoding() 
字符缓冲流（高效流）
BufferedReader：字符缓冲流，从字符输入流中读取文本，缓冲各个字符，从而实现字符、数组和行的高效读取。

            构造方法：
            // 创建一个使用默认大小输入缓冲区的缓冲字符输入流
            BufferedReader(Reader in)
            // 创建一个使用指定大小输入缓冲区的缓冲字符输入流
            BufferedReader(Reader in, int sz)
            特有方法：
            // 读取一个文本行
            String readLine()
                //生成字符缓冲流对象
                BufferedReader reader = new BufferedReader(new InputStreamReader(new FileInputStream("test.txt")));
                String str;
                //一次性读取一行
                while ((str = reader.readLine()) != null) {
                    System.out.println(str);// 爱生活，爱Android
                }

                //关闭流
                reader.close();

* BufferedWriter：字符缓冲流，将文本写入字符输出流，缓冲各个字符，从而提供单个字符、数组和字符串的高效写入。

                构造方法：
                // 创建一个使用默认大小输出缓冲区的缓冲字符输出流
                BufferedWriter(Writer out)
                // 创建一个使用给定大小输出缓冲区的新缓冲字符输出流
                BufferedWriter(Writer out, int sz)
                特有方法：
                // 写入一个行分隔符
                void newLine() 
* FileReader、FileWriter
 FileReader：InputStreamReader类的直接子类，用来读取字符文件的便捷类，使用默认字符编码。 FileWriter：OutputStreamWriter类的直接子类，用来写入字符文件的便捷类，使用默认字符编码。 
#### FileInputStream和FileOutputStream
这是在拷贝文件操作的时候，经常用到的两个类。在处理小文件的时候，它们性能表现还不错，在大文件的时候，最好使用BufferedInputStream (或 BufferedReader) 和 BufferedOutputStream (或 BufferedWriter)
#### Files的常用方法都有哪些？
Files. size()：查看文件个数。
Files. read()：读取文件。
Files. write()：写入文件。
Files. exists()：检测文件路径是否存在。
Files. createFile()：创建文件。
Files. createDirectory()：创建文件夹。
Files. delete()：删除一个文件或目录。
Files. copy()：复制文件。
Files. move()：移动文件。
#### 什么是 java 序列化，如何实现 java 序列化？
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
