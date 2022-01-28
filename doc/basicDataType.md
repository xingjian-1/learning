##### 数据存储单位
位是指二进制数位，是计算机中数据存储的最小单位，字节是计算机中数据存储的基本单位。一字节表示8位。一个汉字是两个字节，也就是16位。字节是二进制数据的单位。一个字节在二进制里面8位长。但是，一些老型号计算机结构使用不同的长度。为了避免混乱，在大多数国际文献中，使用此代替byte。<br>
bit(位，又名'比特'):bit的缩写是b，是计算机中的最小数据单位(属于二进制的范畴，用0或者1表示)<br>
Byte(字节):Byte的缩写是B，是计算机数据存储的基本单位。比如一个字符就是1Byte，如果是汉字，则是2Byte <br>

            1B（字节）=8b（位）
            1 KB = 1024 B
            1 MB = 1024 KB
            1 GB = 1024 MB
            1TB = 1024GB
            
 ![conversion](/img/conversion.png)
 
##### 不同的编码格式占字节数
s.getBytes(encodingName).length

            单个英文字母： 
            字节数：1;编码：GB2312 
            字节数：1;编码：GBK 
            字节数：1;编码：GB18030 
            字节数：1;编码：ISO-8859-1 
            字节数：1;编码：UTF-8 
            字节数：4;编码：UTF-16 
            字节数：2;编码：UTF-16BE 
            字节数：2;编码：UTF-16LE

            单个中文汉字： 
            字节数：2;编码：GB2312 
            字节数：2;编码：GBK 
            字节数：2;编码：GB18030 
            字节数：1;编码：ISO-8859-1 
            字节数：3;编码：UTF-8 
            字节数：4;编码：UTF-16 
            字节数：2;编码：UTF-16BE 
            字节数：2;编码：UTF-16LE

##### 基本数据类型
基本数据类型只有8种，可按照如下分类：
* 整数型
byte：占内存大小为1子节（8位），取值范围-128-127，默认值为0。<br>
short:占内存大小为2子节（16位），取值范围-32768-32767，默认值为0。<br>
int:占内存大小为4子节（32位)，取值范围-2的31次方到2的31次方-1，默认值为0。<br>
long：占内存大小为8子节(64位)，取值范围 -2的63次方到2的63次方-1，默认值为0。<br>
* 浮点型
float(单精度)：占内存大小为4子节(32位)，取值范围：负数 -3.402823E+38到-1.401298E-45，正数：1.401298E-45到3.402823E+38，默认值0.0。<br>
double(双精度)：占内存大小为8子节(64位)，取值范围：负数 -1.797693E+308到-4.9000000E-324，正数：4.9000000E-324到1.797693E+308，默认值0.0。<br>
* 字符型
char：占内存大小为2子节(16位)，取值范围 0-65535(0-2^16-1),用单引号括起来的一个字符也可以是一个中文字符，使用Unicode码代表字符。
* 布尔型
boolean： 占内存大小为1子节(8位)，取值范围 true，false
##### 引用数据类型
引用数据类型非常多，大致包括：类、 接口类型、 数组类型、 枚举类型、 注解类型、 字符串型。例如，String类型就是引用类型。
##### 基本数据类型和引用数据类型区别
基本数据类型存放在栈中。<br>
引用数据类型变量，其具体内容都是存放在堆中的，而栈中存放的是其具体内容所在内存的地址。通过变量地址可以找到变量的具体内容，就如同通过房间号可以找到房间一般。<br>

    public class Main{
       public static void main(String[] args){
           //基本数据类型
           int i=1;//1叫直接量（或字面量），整数字面量默认都为int类型，所以在定义long类型数据后面加L或l。小于32位数的变量，都按int结果结算。
           double d=1.2;//浮点数 字面量默认都为double类型，在定义float类型数据后面加F或f，double类型可不写，但是在小数计算中一定要写D

           //引用数据类型
           String str="helloworld";
       }
    }
 ##### 传递方式  
 * 基本数据类型
 在方法中定义的数据类型变量，调用方法时作为参数是按数值传递的。
 
          //基本数据类型作为方法参数被调用
          public class Main{
             public static void main(String[] args){
                 int msg = 100;
                 System.out.println("调用方法前msg的值：\n"+ msg);    //100
                 fun(msg);
                 System.out.println("调用方法后msg的值：\n"+ msg);    //100
             }
             public static void fun(int temp){
                 temp = 0;
             }
          }
      
 ![baseDate](/img/baseDate.png)
  * 引用类型
  引用数据类型变量，调用方法时作为参数是按引用传递的，传递的是引用的副本   
  
        //引用数据类型作为方法参数被调用
        class Book{
            String name;
            double price;
            public Book(String name,double price){
                this.name = name;
                this.price = price;
            }
            public void getInfo(){
                System.out.println("图书名称："+ name + "，价格：" + price);
            }
            public void setPrice(double price){
                this.price = price;
            }
        }

        public class Main{
           public static void main(String[] args){
               Book book = new Book("Java开发指南",66.6);
               book.getInfo();  //图书名称：Java开发指南，价格：66.6
               fun(book);
               book.getInfo();  //图书名称：Java开发指南，价格：99.9
           }
           public static void fun(Book temp){
               temp.setPrice(99.9);
           }
        }

 ![datetype](/img/datetype.png)
