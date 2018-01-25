#Netty 笔记
## java I/O基础，技术铺垫
### BIO    
* 同步阻塞式I/O：  
    服务端的线程数和客户端并发访问数是1：1的关系,当客户端并发访问数增加后，  
    系统的性能会急剧下降，随着访问量的不断增加，系统会发生线程栈溢出,创建    
    新线程失败，最终会导致服务器宕机等致命性问题。
* 伪异步I/O：   
    为了解决同步阻塞I/O面临的一个链路需要一个线程处理的问题，后端通过一个    
    线程池处理多个客户端的接入。通过线程池可以灵活的调配线程的资源和设置最大   
    线程数，防止海量接入导致线程耗尽。     
### NIO
*   NIO——New I/O NIO——Non black I/O 非阻塞I/O  
*   NIO提供了SocketChannel 和ServerSocketChannel两种不同的套接字通道实现   
    并且这两种通道都支持阻塞和非阻塞。阻塞模式使用方便，性能和可靠性都比较低，非阻塞正   
    好相反。一般来说低负载，低并发的程序可以考虑同步阻塞模式;对于高并发，高负载的可以  
    考虑非阻塞模式
### 缓冲区Buffer  
*   Buffer是一个对象，它包含一些要写入或写出的数据。在面向流编程的I/O模型中，可以直接   
    把数据写入到Stream，或者从Stream中读取。在NIO中所有的数据都要经过缓冲区Buffer   
    处理。Buffer实际上是一个数组，通常是一个字节数组（ByteBuffer），提供对数据的结构  
    化访问和维护读写等信息。
### 通道Channel
*   网络数据通过Channel进行读写。通道和流的区别在于，通道是双向的，流只是从一个方向    
    向一个方向的移动，通道可以同时进行读写。
*   Channel 主要包括两大类：用于网络读写的SelectableChannel和用于文件操作的FileChannel   
### 多路复用器Selector
*   提供选择已经就绪的任务的能力。简单的说就是Selector会不断轮询注册在其上的Channel    
    如果某个channel上发生了读写操作，Channel就会处于就绪状态，会被Selector轮询出来    
    然后获取Channel的集合，进行读写操作。一个多路复用器Selector可以同时轮询多个Channel  
##  为什么选择Netty
*   API简单，开发门槛低   
*   功能强大，预制了多种编解码功能，支持多种主流编码协议
*   定制能力强，可以通过ChannelHandler对通信框架进行灵活烦人扩展
*   性能高，通过与其他主流的NIO框架进行对比，Netty的综合性能最高
*   成熟，稳定Netty修复了JDK NIO BUG
*   社区活跃，版本迭代周期短
*   经历了大规模商业的考验
### TCP的拆包/粘包
*   产生的原因： 
   
    1. 应用程序write写入的字节大小小于套接口发送缓冲区大小    
    2. 进行MSS大小的TCP分段    
    3. 以太网帧的payload大于MTU进行IP分片  
    
*   粘包问题的解决方案：  
    
    1. 消息定长，例如每个报文的长度大小固定为200字节，不够的空位补空格。   
    2. 在包尾处加入回车换行符进行分割，例如FTP协议；   
    3. 将消息分为消息头和消息体，消息头中包含表示消息总长度（或者消息体长度）的字段，通常设计思路   
    为消息头的第一个字段使用int32来表示消息的总长度。
    4. 更复杂的应用层协议   
*   Netty提供的解析器
    1. StringDecoder 把消息转换成String串
    2. LineBasedFrameDecoder以换行符进行分割
    3. DelimiterBasedFrameDecoder 以特殊字符作为消息结束的分隔符
    4. FixedLengthFrameDecoder 固定消息长度

### 编解码技术
*   Java 自带的序列化 Serializable接口
    1. 效率低
    2. 序列化后的码流比较大
    3. 不能跨语言
*   MessagePack 
    1. 高效的二进制序列化框架
    2. 序列化后的码流比较小
    3. 跨语言
*   Google Protobuf 
    1. Google内部长期使用，成熟度比较高
    2. 序列化后的码流比较小，更利于传输
    3. 跨语言
    4. 编解码的性能较高
    5. 支持不同协议版本的向前兼容
    6. 支持定义可选和必须字段
*   JBoss Marshalling
    1. 对Jdk默认序列化的优化
    
##源码分析
  
### ByteBuf 

*   Netty自己提供了缓冲区ByteBuffer的实现：ByteBuf 

*   ByteBuffer的缺点：
     1. ByteBuffer长度固定，一旦分配完成，它的容量不能动态扩展和收缩，当编码的POJO  
     对象大于ByteBuffer的容量时，会发生索引越界异常。
     2. ByteBuffer只有一个标识位置的指针position，读写的时候需要手动调用flip（）或rewind（），
     使用者必须小心谨慎的使用API。  
     3. ByteBuffer的API功能有限，一些高级特性不支持，需要自己实现。  
*   ByteBuf的基本功能：
    1. 7种Java基本类型（除了boolean），byte数组，ByteBuffer（ByteBuf）的读写。
    2. 缓冲区自身的copy和slice等。
    3. 设置网络字节序。
    4. 构造缓冲区实例。
    5. 操作位置指针等方法。
    6. 参考JDK ByteBuffer的实现，增加额外的功能，解决ByteBuffer的缺点。
    7. 聚合JDK ByteBuffer，通过Facade模式对其进行包装，可以减少自身代码量，降低实现成本   
*   实现原理：    
        ByteBuffer每次都要flip（），ByteBuf每次读取的都是readIndex和WriteIndex之间的数据，   
        没进行一次读操作都会移动readIndex，每次写操作都会移动writeIndex,readIndex从0开始，   
        总是小于WriteIndex。
        
*   使用细节：   
    参见Netty权威指南ByteBuf章节
    
### Channel

*   Channel功能说明：    

    Netty网络操作抽象类，它聚合了一组功能，包括但不仅限于网络读写，客户端发起的网络连接，主动关闭连接，    
    链路关闭，获取双方的通信地址。也包括了Netty框架一些相关的功能，获取该Channel的EventLoop，获取   
    缓冲分配器的ByteBufAllocator和pipeline等。
    
*   JDK Channel的劣势
    1. 没有统一的Channel接口供业务开发者使用，对于用户而言没有统一的操作视图，使用起来不方便。
    2. 主要职责是网络I/O操作，由于他们是SPI接口，由具体虚拟机厂商提供，所以通过继承SPI功能来  
    扩展其功能，难度很大。
    3. Netty的Channel需要能够和Netty的整体架构融合到一起，这些JDK Channel没有提供，需要重新开发
    4.自定义的Channel，功能实现更加灵活。
*   设计理念：   
    1. 在Channel接口层，采用Facade模式进行统一封装，将网络I/O操作，网络I/O相关联的其他操作封装起来，统一对外提供
    2. Channel接口的定义尽量大而全，不同的子类实现不同的功能，公共功能在抽象父类中实现，最大程度的实现功能和接口的重用。 
    3. 具体采用聚合而非包含的方式，将相关的功能包含在Channel中，由Channel统一分配调度，功能实现更加灵活。
    
*   功能介绍——网络I/O相关的：  
    1. Channel read（）:从当前Channel读取数据到第一个缓冲区inbound缓冲区中，如果数据被成功读取，触发     
       channelRead（ChannelHandlerContext，Object）事件。读取操作API调用完成之后，紧接着会触发     
       ChannelHandler.channelReadComplete(ChannelHandlerContext)事件,这样业务的ChannelHandler   
       可以决定是否继续读取数据。如果已经有读操作被挂起，则后去的读操作会忽略。   
    2. ChannelFuture write(Object msg):请求当前的msg通过ChannelPipeline写入到目标Channel中。注意，
       write操作只是将消息存入到消息发送环形数组中，并没有真正被发送，只有调用flush操作才会写入Channel中，发送给对方。    
    3. ChannelFuture writeAndFlush 等价于read（）+flush（）    
    4. ChannelFuture close()：主动关闭当前连接。
    5. ChannelFuture disconnect 请求断开与远程端对端连接
    6. ChannelFuture connect 发起连接
*   功能介绍——其他常用API说明；
    1. eventLoop():Channel注册到EventLoop的多路复用器上，用于处理I/O事件。在Netty中，它不仅用于处理网络事件，    
       也可用来执行定时任务和用户自定义的NIOTask等任务。
    2. metadata（）：获取当前Channel的TCP配置。
    
### ChannelPipeline
*   功能说明：   
    是ChannelHandler的容器，负责ChannelHandler的管理和事件拦截和调度。
*   ChannelPipeline 的事件管理   
    pipeline 中所有以fireXXX开头的方法,都是从I/O线程流向用户业务Handler的inbound事件
### ChannelHandler
*   功能说明：
    负责对I/O事件或者I/O操作进行拦截和处理，它可以选择性的拦截和处理自己感兴趣的事件，也可以透传和终止事件的传递。  
*   注解:
    1. Sharable：多个ChannelPipeline共用一个ChannelHandler
    2. Skip：被Skip注解的方法不会被调用，可以直接忽略。
### ChannelFuture  
*   Future功能    
    用于代表异步操作的结果
*   状态：
    1. completed
    2. uncompleted
*   结果：
    1. 操作成功
    2. 操作失败
    3. 操作被取消
    
*   ChannelFuture提供了一系列的新API，用于获取操作结果，添加事件监听器，取消I/O操作，同步等待。
### Promise
*   功能介绍：
    Promise是可写的Future，Future本身并没有写操作的接口，Netty通过Promise对Future进行扩展，用于设置  
    I/O操作的结果