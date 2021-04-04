#### 哈希表 
`[哈希表]`是一种数据结构，提供了键key和值value的映射关系。哈希表本质上是一个数组，不过数组是根据下标，像a[0]、a[1]、a[2]这样来访问，而哈希表的key一般是以字符串为主，通过哈希函数，可以把字符串类型或者其他类型的key，转化成数组的下标index。例如：

      给出一个长度为8的数组
      当key=0011011时，index=HashCode("0011011")%Array.length=7
      当key=this时, index=HashCode("this")%Array.length=6
<br><img src="/img/哈希映射关系.png" width = "30%" height = "40%" alt="图片名称" align=center />

#### 哈希冲突 
`哈希冲突`不同的key通过哈希函数获取的下标有可能是相同的，例如0011011这个key对应的数组下标是2,0022011这个key对应的数组下标也是2，这种情况就是哈希冲突。<br>
`如何解决哈希冲突`链表法：用HashMap，存储结构是数组+链表+红黑树(jdk1.8)。<br><img src="/img/20200813125140.png" width = "50%" height = "30%" alt="图片名称" align=center ><br>
##### 两个对象的 hashCode()相同，则 equals()也一定为true，对吗？
      不对，两个对象的hashCode值相同，内容不一定相同。
      String str1 = "通话";
      String str2 = "重地";
      System. out. println(String. format("str1：%d | str2：%d",  str1. hashCode(),str2. hashCode()));
      System. out. println(str1. equals(str2));
      执行的结果：
      str1：1179395 | str2：1179395
      false
      '通话'和'重地'的哈希值相同，但是内容不同，在散列表中，hashCode()相等即两个键值对的哈希值相等，然而哈希值相等，并不一定能得出键值对相等。
数据结构操作演示地址：https://www.cs.usfca.edu/~galles/visualization/Algorithms.html
