##### I/O 的定义
在计算机系统中I/O就是输入（Input）和输出(Output)的意思，针对不同的操作对象，可以划分为磁盘I/O模型，网络I/O模型，内存映射I/O, Direct I/O、数据库I/O等，只要具有输入输出类型的交互系统都可以认为是I/O系统，也可以说I/O是整个操作系统数据交换与人机交互的通道，这个概念与选用的开发语言没有关系，是一个通用的概念。对于一次I/O操作，必然涉及2个参与方，一个输入端，一个输出端，而又根据参与双方的设备类型，我们又可以分为磁盘I/O，网络I/O（一次网络的请求响应，网卡）等。
##### IO模型之阻塞I/O(BIO)
应用程序中进程在发起IO调用后至内核执行IO操作返回结果之前，若发起系统调用的线程一直处于等待状态，则此次IO操作为阻塞IO。阻塞IO简称BIO，Blocking IO。<br>
因此BIO带来了一个问题：如果内核数据需要耗时很久才能准备好，那么用户进程将被阻塞，浪费性能。为了提升应用的性能，虽然可以通过多线程来提升性能，但线程的创建依然会借助系统调用，同时多线程会导致频繁的线程上下文的切换，同样会影响性能。
##### IO模型之阻塞I/O(NIO)
解决方案那就是用户进程在发起系统调用时指定为非阻塞，内核接收到请求后，就会立即返回，然后用户进程通过轮询的方式来拉取处理结果。应用程序中进程在发起IO调用后至内核执行IO操作返回结果之前，若发起系统调用的线程不会等待而是立即返回，则此次IO操作为非阻塞IO模型。
