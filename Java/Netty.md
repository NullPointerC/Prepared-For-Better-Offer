### BIO、NIO、AIO分别是什么?

BIO是同步阻塞lO，NIO是同步非阻塞lO，AIO异步非阻塞lO；

BIO：

![image-20220311232951248](http://static.codenote.xyz/img/20220311232951.png)



NIO：

![image-20220312133301603](http://static.codenote.xyz/img/20220312133301.png)

多路复用：

![image-20220312133452993](http://static.codenote.xyz/img/20220312133453.png)

把轮询的操作转移到系统调用上。

AIO：

![image-20220312133658570](http://static.codenote.xyz/img/20220312133658.png)

linux只支持文件的aio，不支持网络的aio，所以aio没有nio那么流行；

### select、poll、epoll的机制有了解吗？

都是I/O多路复用的实现机制，实现一个进程监视多个文件描述符FD。

在linux中一切皆文件，比如scoket，进程，磁盘都被抽象成文件。

select：

传入所有的fd，系统会返回文件的状态，应用程序可以根据返回的状态，进行下一步操作。本质上就是把用户空间的fd复制到内核空间中，并且select限制了单个进程的最大文件描述符数量为1024。

poll：

本质上和select一致，区别在于没有了最大文件描述符数量的限制。

epoll：

是二者的增强版，解除了数量限制，也解决了每次需要重复传入所有fd的问题，在内核中分配了一块空间，将文件描述符的事件存放在事件表中，后续只需对事件表crud即可。

### 说说你对Netty的了解?

Netty是由JBOSS提供的一个Java开源框架。高性能异步事件驱动的的NIO框架，提供了TCP，UDP，HTTP的协议栈。

### Netty跟Java NIO有什么不同，为什么不直接使用JDK NIO类库?

NIO开发的基本流程如下所示：

![image-20220312135632694](http://static.codenote.xyz/img/20220312135632.png)

### Netty组件有哪些?

ChannelHandler及其实现类；

ChannelPipeline；

ChannelHandlerContext；

ChannelFuture；

ServerBootstrap和 Bootstrap；

EventLoopGroup和 NioEventLoopGroup；

![image-20220312140538122](http://static.codenote.xyz/img/20220312140538.png)

### Netty如何做到高性能?Netty的线程模型是怎么样的?

高性能体现的三大要素:传输、内存、线程。

1.异步非阻塞IO类库，解决了大量请求下，一个服务端无法应对大量增长的IO请求；

2.Reactor模式解决了用户数量增长导致资源分配不可控的问题；

3.TCP接收缓冲区使用直接内存替换了堆内存，避免内存之间的复制，运用零拷贝提升了I/O读取和写入的性能；

4.支持通过内存池来循环利用byteBuf；

5.可配置的I/O线程数、TCP参数；

6.环形数组缓冲区替换了锁的方式；

7.使用线程安全容器,原子类；

8.关键资源的处理使用单线程串行化的方式；

9.通过引用计数器及时的申请释放不再被引用的对象，降低了GC的频率；

