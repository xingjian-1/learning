#### Socket通信原理
TCP/IP（Transmission Control Protocol/Internet Protocol）即传输控制协议/网间协议，是一个工业标准的协议集，它是为广域网（WANs）设计的。
 UDP（User Data Protocol，用户数据报协议）是与TCP相对应的协议。它是属于TCP/IP协议族中的一种。
TCP/IP协议族包括运输层、网络层、链路层

Socket是应用层与TCP/IP协议族通信的中间软件抽象层，它是一组接口。在设计模式中，Socket其实就是一个门面模式，它把复杂的TCP/IP协议族隐藏在Socket接口后面，对用户来说，一组简单的接口就是全部，让Socket去组织数据，以符合指定的协议。

###### TCP 三次握手
* 第一握：首先客户端发送一个syn，请求连接，
* 第二握：服务器收到之后确认，并发送一个 syn ack应答
* 第三握：客户端接收到服务器发来的应答之后再给服务器发送建立连接的确定


* 客户端socket对象connect调用之后进行阻塞，此过程发送了一个syn。
* 服务器内核完成三次握手，即发送syn和ack应答。
* 客户端socket对象收到服务端发送的应答之后，再发送一个ack给服务器，并返回connect调用，建立连接。
* 服务器socket对象接受客户端最后一次握手确定ack建立连接。
* 此时服务端调用accept，则从连接队列中将之前建立的连接取出返回。


###### 客户端基本的流程:
* 创建Socket(socket)
* 和服务器建立连接(connect)
* 向服务器发送请求(write)
* 接受服务器端的数据(read)
* 关闭连接(close)

###### 服务器端基本的流程:
* 创建Socket(socket)
* 绑定端口(bind)
* 监听端口(listen)
* 等待客户端请求(客户端没有请求时阻塞)(accept)
* 接受客户端请求(read)
* 向客户端发送数据(write)
* 关闭socket(close)
