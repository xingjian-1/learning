##### Public Protected Private 三个访问修饰符的区别
将成员属性声明为private，而将成员方法声明为public,有时候，类外部可能要操作到某些私有数据成员，那么就可以增加一个公有的方法，再由这个方法来操作私有数据，避免因类外部的误操作而造成的数据损坏。
* 在同一个包不同类
* 在不同的包的子类
##### transient
transient关键字一般在实现了Serializable接口的类中使用，被transient修饰的变量不参与序列化和反序列化。
