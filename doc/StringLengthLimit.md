##### String是否有长度限制
在Java中String是有长度限制的，在JVM编译中还有规范。<br>
String长度限制的场景（将某固定文件转码成Base64的形式用字符串存储，在运行时需要的时候在转回来，当时文件比较大），那这个规范限制到底是怎么样的 如下:
##### String是怎么存储字符串的
String使用一个char类型的数组来存储字符串中的字符的。
String既然是数组存储那数组会有长度的限制吗？是的有限制，但是是在有先提条件下的，String中返回length的方法返回值类型是int类型，Java中定义数组是可以给数组指定长度的，当然不指定的话默认会根据数组元素来指定。

        int[] arr1 = new int[10]; // 定义一个长度为10的数组
        int[] arr2 = {1,2,3,4,5}; // 那么此时数组的长度为5
通过源码来看看int类型对应的包装类Integer可以看到，其长度最大限制为2^31 -1，那么说明了数组的长度是0~2^31-1，那么计算一下就是（2^31-1 = 2147483647 = 4GB）<br>
String a = "ssssssss...";构造的10万个字符的字符串，编译之后虚拟机提示报错，说我们的字符串长度过长，不是说好了可以存21亿个吗？为什么才10万个就报错了呢？
这里涉及到了JVM编译规范的限制了，其实JVM在编译时，如果我们将字符串定义成了字面量的形式，编译时JVM是会将其存放在常量池中，这时候JVM对这个常量池存储String类型做出了限制。

在class文件中u2表示的是无符号数占2个字节单位，我们知道1个字节占8位，2个字节就是16位 ，那么2个字节能表示的范围就是2^16- 1 = 65535 。范中class文件格式对u1、u2的定义的解释做了一下摘要：
##### 总结
首先字符串的内容是由一个字符数组 char[] 来存储的，由于数组的长度及索引是整数，且String类中返回字符串长度的方法length() 的返回值也是int ，所以通过查看java源码中的类Integer我们可以看到Integer的最大范围是2^31 -1,由于数组是从0开始的，所以数组的最大长度可以使【0~2^31-1】通过计算是大概4GB。
但是通过翻阅java虚拟机手册对class文件格式的定义以及常量池中对String类型的结构体定义我们可以知道对于索引定义了u2，就是无符号占2个字节，2个字节可以表示的最大范围是2^16 -1 = 65535。
其实是65535，但是由于JVM需要1个字节表示结束指令，所以这个范围就为65534了。超出这个范围在编译时期是会报错的，但是运行时拼接或者赋值的话范围是在整形的最大范围。
