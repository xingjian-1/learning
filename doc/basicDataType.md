##### 基本数据类型
基本数据类型只有8种，可按照如下分类：
* 整数型
byte：占内存大小为1子节（8位），取值范围-128-127，默认值为0。<br>
short:占内存大小为2子（节16位），取值范围-32768-32767，默认值为0。<br>
int:占内存大小为4子节（32位)，取值范围-2的31次方到2的31次方-1，默认值为0。<br>
long：占内存大小为8子节(64位)，取值范围 -2的63次方到2的63次方-1，默认值为0。<br>
* 浮点型
float(单精度)：占内存大小为4子节(32位)，取值范围：负数 -3.402823E+38到-1.401298E-45，正数：1.401298E-45到3.402823E+38，默认值0.0。<br>
double(双精度)：占内存大小为8子节(64位)，取值范围：负数 -1.797693E+308到-4.9000000E-324，正数：4.9000000E-324到1.797693E+308，默认值0.0。<br>
* 字符型
char：占内存大小为2子节(16位)，取值范围 0-65535
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
           int i=1;
           double d=1.2;

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
