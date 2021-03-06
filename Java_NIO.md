#### 参考文章

1. 《Java编程思想》
2. 《深入分析Java Web技术内幕》 
3. 《unix环境高级编程》
4. 《unix网络编程》
3. 《UNIX网络编程（卷1）：套接字联网API（第3版）》
3. [如何学习Java的NIO？ - 知乎](https://www.zhihu.com/question/29005375)
1. [深入浅出NIO Channel和Buffer-占小狼](http://www.jianshu.com/p/052035037297)
2. [深入浅出NIO Socket实现机制-占小狼](http://www.jianshu.com/p/0d497fe5484a)
3. [Netty源码分析-占小狼](http://www.jianshu.com/nb/7269354)
3. [Java NIO浅析-美团技术](https://zhuanlan.zhihu.com/p/23488863)
4. [Java I/O底层是如何工作的？](http://www.importnew.com/14111.html)
5. [理解Java NIO-云栖社区]()
6. [Java NIO 系列教程-并发编程网]()
7. [Linux 中的零拷贝技术，第 1 部分](https://www.ibm.com/developerworks/cn/linux/l-cn-zerocopy1/)
8. [Linux 中的零拷贝技术，第 2 部分](https://www.ibm.com/developerworks/cn/linux/l-cn-zerocopy2/)
9. [Java NIO编程实例之一Buffer - 知乎专栏](https://zhuanlan.zhihu.com/p/25701512)
10. [Java NIO编程实例之二Channel - 知乎专栏](https://zhuanlan.zhihu.com/p/25914350)
11. [Java NIO编程实例之三Selector - 知乎专栏](https://zhuanlan.zhihu.com/p/26243285)
12. [多种I/O模型及其对socket效率的改进](http://mickhan.blog.51cto.com/2517040/1586370)
13. [谁能用通俗的语言解释一下什么是 RPC 框架？-知乎](https://www.zhihu.com/question/25536695/answer/36197244)
13. [浅谈 Linux 中 Selector 的实现原理](http://www.jianshu.com/p/2b71ea919d49)
13. [linux下epoll如何实现高效处理百万句柄的](http://blog.csdn.net/russell_tao/article/details/7160071)
13. []()
13. []()
13. []()



## NIO

### 《深入分析Java Web技术内幕》page202 Java NIO使用到内存

新I/O（NIO）基于**通道和缓冲区**。

Java堆内存支持IO缓冲区->
NIO使用`java.nio.ByteBuffer.allocateDirect()`分配内存——NIO direct memory。

1. `java.nio.ByteBuffer.allocateDirect()`分配内存使用的是**本机内存**而不是Java堆内存，说明每次分配内存时会调用操作系统的`os::malloc()`函数。
2. ByteBuffer产生的数据和网络或者磁盘交互，都在操作系统的**内核空间**，不需要复制到用户空间（Java内存）中。显然比要从OS内核空间到Java堆上的切换操作快，*避免在Java堆和本机堆之间复制数据*。

ByteBuffer的GC：
1. GC仅在Java堆被填满，以至于无法为堆分配请求提供服务时发生；
2. GC在Java应用程序中显式请求时发生——显式调用`System.gc()`。


### [如何学习Java的NIO？ - 知乎](https://www.zhihu.com/question/29005375)

Java NIO只是各个操作系统上的非阻塞IO和IO多路复用的跨平台API，理解Java NIO离不开对非阻塞IO概念的引入和操作系统的实现的理解。 我觉得Java的NIO要从几个层面来理解。

1 问题域
网络协议的解析，TCP拆包、粘包

远程过程调用RPC
网络通讯的一种常见场景

可参见：
谁能用通俗的语言解释一下什么是 RPC 框架？

C10K问题
在有1万个同时连接时，传统的阻塞的，每个连接一个线程的网络处理方式存在的问题。如果没有这个问题，非阻塞IO没有那么迫切和广泛的需求。
可以参见：
http://www.kegel.com/c10k.html

2 操作系统级
硬件IO的工作机制，设备控制器，缓冲区，驱动程序，轮询，中断，DMA

IO的几种模型，同步阻塞，同步非阻塞，异步，各有什么特点
可参见
6.2 I/O Models （Unix网络编程一书的章节，英文版，搜关键词可以找到翻译版）

IO多路复用/IO multiplexing，select/poll/epoll等非阻塞IO方法

可参见：
I/O多路复用技术（multiplexing）是什么？ - 知乎
epoll到底是如何做到少量线程同时接收多个请求事件处理部分到底放在哪里？ - 知乎
为什么select打开的FD数量有限制，而poll、epoll等打开的FD数量没有限制？ - 知乎
epoll到底是如何做到少量线程同时接收多个请求事件处理部分到底放在哪里？ - 知乎

内核空间，用户空间，系统调用，深入的话需要了解CPU保护模式，物理地址空间/虚拟地址空间。

内存映射文件/mmap

操作系统中线程的实现和上下文切换的开销
可参见：
如何深入理解多进程，多线程，非阻塞/异步IO(callback) 以及Coroutine模型？

3 Java NIO
Buffer，Channel，Selector都是对操作系统系统调用和类库的封装。

了解这些是怎么使用的。

Java内存管理和堆外内存的使用

面向流的IO与面向缓冲区的IO

4 Netty
Netty为Java开发网络程序提供了进一步的封装。
可参见：
通俗地讲，Netty 能做什么？

Reactor模式，这个和多路复用相关

Pipline/责任链模式，提高网络协议解析到可重用性。

总结
如果能够从Java API向下看到操作系统，甚至处理器，向上看到应用框架和设计模式，就容易形成更清晰的理解。
通常来说，理解了为什么和怎么工作的，才能加深对是什么和怎么用的理解。

### [Java I/O底层是如何工作的？](http://www.importnew.com/14111.html)

缓存处理->内核、用户空间->虚拟内存->内存分页->文件、块IO

**缓冲与缓冲的处理方式**,是所有I/O操作的基础。

术语“输入、输出”只对数据移入和移出缓存有意义。任何时候都要把它记在心中。通常，进程执行操作系统的I/O请求包括数据从缓冲区排出（写操作）和数据填充缓冲区（读操作）。这就是I/O的整体概念。

首先，进程要求其缓冲通过read()系统调用填满。这个系统调用导致内核向磁盘控 制硬件发出一条命令要从磁盘获取数据。磁盘控制器通过DMA直接将数据写入内核的内存缓冲区，不需要主CPU进一步帮助。当请求read()操作时，一旦磁盘控制器完成了缓存的填 写，内核从内核空间的临时缓存拷贝数据到进程指定的缓存中。

从内核空间拷贝到最终用户缓存看起来增加了额外的工作。为什么不告诉磁盘控制器直接发送数据到用户空间的缓存呢？好吧，这是由虚拟内存实现的。

通过将内核空间地址映射到相同的物理地址作为一个用户空间的虚拟地址，DMA硬件（只能访问物理内存地址）可以填充缓存。这个缓存同时对内核和用户空间进程可见。

所有的磁盘I/O操作都是在**页面级别**上完成的。这是数据在现代分页操作系统上在磁盘与物理内存之间移动的唯一方式。

一个文件系统组织（在硬盘中）了一系列均匀大小的数据块。有些块保存元信息，如空闲块的映射、目录、索引等。其它块包含实际的文件数据。

分页文件系统执行I/O可以归结为以下逻辑步骤：

确定请求跨越了哪些文件系统分页（磁盘段的集合）。磁盘上的文件内容及元数据可能分布在多个文件系统页面上，这些页面可能是不连续的。
分配足够多的内核空间内存页面来保存相同的文件系统页面。
建立这些内存分页与磁盘上文件系统分页的映射。
对每一个内存分页产生分页错误。
虚拟内存系统陷入分页错误并且调度pagins（页面调入），通过从磁盘读取内容来验证这些页面。
一旦pageins完成，文件系统分解原始数据来提取请求的文件内容或属性信息。

文件锁有两种形式：共享和独占。多个共享锁可以同时在相同的文件区域有效。另一方面，独占锁要求没有其它锁对请求的区域有效。

流I/O，它是管道的原型，必须顺序访问I/O数据流的字节。常见的数据流有TTY（控制台）设备、打印端口和网络连接。

数据流通常但不一定比块设备慢，提供间歇性输入。大多数操作系统允许在非阻塞模式下工作。允许一个进程检查数据流的输入是否可用，不必在不可用时发生阻塞。这种管理允许进程在输入到达时进行处理，在输入流空闲时可以执行其他功能。

### [Java NIO浅析-美团技术](https://zhuanlan.zhihu.com/p/23488863)

NIO（Non-blocking I/O，在Java领域，也称为New I/O），是一种同步非阻塞的I/O模型，也是I/O多路复用的基础，已经被越来越多地应用到大型应用服务器，成为解决高并发与大量连接、I/O处理问题的有效方式。

传统上，BIO+线程池，严重依赖于线程。但线程是很"贵"的资源。

所有的系统I/O都分为两个阶段：等待就绪和操作。举例来说，读函数，分为等待系统可读和真正的读；同理，写函数分为等待网卡可以写和真正的写。

需要说明的是等待就绪的阻塞是不使用CPU的，是在“空等”；而真正的读写操作的阻塞是使用CPU的，真正在"干活"，而且这个过程非常快，属于memory copy，带宽通常在1GB/s级别以上，可以理解为基本不耗时。

NIO一个重要的特点是：socket主要的读、写、注册和接收函数，在等待就绪阶段都是非阻塞的，真正的I/O操作是同步阻塞的（消耗CPU但性能非常高）。

NIO的读写函数可以立刻返回，这就给了我们不开线程利用CPU的最好机会：如果一个连接不能读写（socket.read()返回0或者socket.write()返回0），我们可以把这件事记下来，记录的方式通常是在Selector上注册标记位，然后切换到其它就绪的连接（channel）继续进行读写。

NIO的主要事件有几个：读就绪、写就绪、有新连接到来。

NIO由原来的阻塞读写（占用线程）变成了单线程轮询事件，找到可以进行读写的网络描述符进行读写。除了事件的轮询是阻塞的（没有可干的事情必须要阻塞），剩余的I/O操作都是纯CPU操作，没有必要开启多线程。

仔细分析一下我们需要的线程，其实主要包括以下几种：

1. 事件分发器，单线程选择就绪的事件。
2. I/O处理器，包括connect、read、write等，这种纯CPU操作，一般开启CPU核心个线程就可以。
3. 业务线程，在处理完I/O后，业务一般还会有自己的业务逻辑，有的还会有其他的阻塞I/O，如DB操作，RPC等。只要有阻塞，就需要单独的线程。
总结

NIO客户端应用：

- 每连接顺序请求的Redis
- 多连接短连接的HttpClient
- 常见的RPC框架，如Thrift，Dubbo
    - 这种框架内部一般维护了请求的协议和请求号，可以维护一个以请求号为key，结果的result为future的map，结合NIO+长连接，获取非常不错的性能。

最后总结一下到底NIO给我们带来了些什么：

1. 事件驱动模型
2. 避免多线程
3. 单线程处理多任务
4. 非阻塞I/O，I/O读写不再阻塞，而是返回0
5. 基于block的传输，通常比基于流的传输更高效
6. 更高级的IO函数，zero-copy
7. IO多路复用大大提高了Java网络应用的可伸缩性和实用性


### 1. [深入浅出NIO Channel和Buffer-占小狼](http://www.jianshu.com/p/052035037297)

Java NIO 由以下几个核心部分组成：

1. Buffer
2. Channel
3. Selector

#### Buffer

一块缓存区，内部使用字节数组存储数据，并维护几个特殊变量，实现数据的反复利用。

几个特殊的变量有：mark, position, limit, capacity

方法有：mark(), reset(), clear(), flip(), rewind()

目前Buffer的实现类有以下几种：

- ByteBuffer
- CharBuffer
- DoubleBuffer
- FloatBuffer
- IntBuffer
- LongBuffer
- ShortBuffer
- MappedByteBuffer

#### ByteBuffer

- HeapByteBuffer：通过初始化字节数组hd，在虚拟机堆上申请内存空间。
- DirectByteBuffer：DirectByteBuffer通过unsafe.allocateMemory在物理内存中申请地址空间（非jvm堆内存），并在ByteBuffer的address变量中维护指向该内存的地址。unsafe.setMemory(base, size, (byte) 0)方法把新申请的内存数据清零。 

#### Channel

又称“通道”，NIO把它支持的I/O对象抽象为Channel，类似于原I/O中的流（Stream），但有所区别：

- 流是单向的，通道是双向的，可读可写。
- 流读写是阻塞的，通道可以异步读写。
- 流中的数据可以选择性的先读到缓存中，通道的数据总是要先读到一个缓存中，或从缓存中写入。

目前已知Channel的实现类有：

- FileChannel
- DatagramChannel
- SocketChannel
- ServerSocketChannel

#### FileChannel

FileChannel的read、write和map通过其实现类FileChannelImpl实现。

### 2. [深入浅出NIO Socket实现机制-占小狼](http://www.jianshu.com/p/0d497fe5484a)

以前基于net包进行socket编程时，accept方法会一直阻塞，直到有客户端请求的到来，并返回socket进行相应的处理。整个过程是流水线的，处理完一个请求，才能去获取并处理后面的请求；当然我们可以把获取socket和处理socket的过程分开，一个线程负责accept，线程池负责处理请求。

NIO为我们提供了更好的解决方案，采用选择器（Selector）找出已经准备好读写的socket，并按顺序处理，基于通道（Channel）和缓冲区（Buffer）来传输和保存数据。

为了实现Selector管理多个SocketChannel，必须将多个具体的SocketChannel对象注册到Selector对象，并声明需要监听的事件，目前有4种类型的事件：

- connect：客户端连接服务端事件，对应值为SelectionKey.OP_CONNECT(8)
- accept：服务端接收客户端连接事件，对应值为SelectionKey.OP_ACCEPT(16)
- read：读事件，对应值为SelectionKey.OP_READ(1)
- write：写事件，对应值为SelectionKey.OP_WRITE(4)

当SocketChannel有对应的事件发生时，Selector能够觉察到并进行相应的处理。

#### epoll原理

epoll是Linux下的一种IO多路复用技术，可以非常高效的处理数以百万计的socket句柄。

使用c封装的3个epoll系统调用：

1. int epoll_create(int size)
epoll_create建立一个epoll对象。参数size是内核保证能够正确处理的最大句柄数，多于这个最大数时内核可不保证效果。
2. int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event)
epoll_ctl可以操作epoll_create创建的epoll，如将socket句柄加入到epoll中让其监控，或把epoll正在监控的某个socket句柄移出epoll。
3. int epoll_wait(int epfd, struct epoll_event *events,int maxevents, int timeout)
epoll_wait在调用时，在给定的timeout时间内，所监控的句柄中有事件发生时，就返回用户态的进程。

epoll的两种工作模式：

1. LT：level-trigger，水平触发模式，只要某个socket处于readable/writable状态，无论什么时候进行epoll_wait都会返回该socket。
2. ET：edge-trigger，边缘触发模式，只有某个socket从unreadable变为readable或从unwritable变为writable时，epoll_wait才会返回该socket。

为了实现client和server的数据交互，Linux下采用管道pipe实现，windows下采用两个socket之间的通信进行实现，它们都有这样的特性：

1. 都有两个端，一个是read端，一个是write端，windows中两个socket也是read和write的角色。
2. 当往write端写入 数据，则read端即可以收到数据。
