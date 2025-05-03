## 1. Hello World

服务端：

```java
// 创建一个 ServerBootstrap 实例，用于引导（bootstrap）和配置服务器的各项参数
new ServerBootstrap()
    // 为服务器分配一个 EventLoopGroup
    .group(new NioEventLoopGroup())
    // 指定服务器通道类型为 NioServerSocketChannel，它基于 Java NIO 的 ServerSocketChannel
    .channel(NioServerSocketChannel.class)
    // boss 负责处理连接，worker（child）负责处理读写
    // 决定了worker（child）能执行哪些操作（handler）
    .childHandler(
    // Channel 和客户端进行数据读写的通道； Initializer 初始化，添加别的 handler
    new ChannelInitializer<NioSocketChannel>() {
        protected void initChannel(NioSocketChannel ch) {
            // 添加具体的 handler
            // 添加解码器，将 byteBuf 转为字符串
            ch.pipeline().addLast(new StringDecoder());
            // 添加自定义的handler
            ch.pipeline().addLast(new ChannelInboundHandlerAdapter() {
                @Override
                // 处理读事件
                public void channelRead(ChannelHandlerContext ctx, Object msg) {
                    System.out.println(msg);
                }
            });
        }
    })
    // 绑定端口
    .bind(8080);
log.debug("服务器已启动...");
```

客户端：

```java
// 创建启动类
new Bootstrap()
    // 为客户端分配一个 EventLoopGroup
    .group(new NioEventLoopGroup())
    // 指定客户端通道类型为 NioSocketChannel，它基于 Java NIO 的 SocketChannel
    .channel(NioSocketChannel.class)
    // 添加处理器（handler）
    .handler(
    // 连接建立时被调用，执行 initChannel 方法
    new ChannelInitializer<>() {
        @Override
        protected void initChannel(Channel ch) {
            // 设置编码器
            ch.pipeline().addLast(new StringEncoder());
        }
    })
    // 连接到服务器
    .connect("127.0.0.1", 8080)
    .sync()
    .channel()
    // 向服务器发送数据
    .writeAndFlush(new Date() + ": hello world!");
log.debug("客户端启动");
```

步骤：

1. 服务器端：创建`ServerBootstrap`类，引导创建和配置
1. 服务器端：设置线程循环组
1. 服务器端：指定通道类型
1. 服务器端：设置处理器`handler`
1. 服务器端：添加一个`handler`
1. 服务器端：绑定监听端口
1. 客户端：创建`Bootstrap`启动类，引导创建和配置
1. 客户端：设置线程循环组
1. 客户端：设置通道类型
1. 客户端：添加处理器`handler`
1. 客户端：连接到服务器
1. 客户端&服务器端：连接建立，调用`initChannel`方法，将`handler`添加到通道中
1. 客户端：`sync`阻塞方法，等待连接建立
1. 客户端：`channel`获取连接对象
1. 客户端：发送数据“hello”
1. 客户端：进入`handler`处理 -- 将字符串转为`ByteBuf`
1. 服务器端：某个`EventLoop`处理`read`事件
1. 服务器端：`handler`将`ByteBuf`转为字符串
1. 服务器端：自定义`handler`将字符串打印



> 一开始需要树立正确的观念
>
> - 把 channel 理解为数据的通道
> - 把 msg 理解为流动的数据，最开始输入是 ByteBuf，但经过 pipeline 的加工，会变成其它类型对象，最后输出又变成 ByteBuf
> - 把 handler 理解为数据的处理工序
>   - 工序有多道，合在一起就是 pipeline，pipeline 负责发布事件（读、读取完成...）传播给每个 handler， handler 对自己感兴趣的事件进行处理（重写了相应事件处理方法）
>   - handler 分 Inbound 和 Outbound 两类
> - 把 eventLoop 理解为处理数据的工人
>   - 工人可以管理多个 channel 的 io 操作，并且一旦工人负责了某个 channel，就要负责到底（绑定）
>   - 工人既可以执行 io 操作，也可以进行任务处理，每位工人有任务队列，队列里可以堆放多个 channel 的待处理任务，任务分为普通任务、定时任务
>   - 工人按照 pipeline 顺序，依次按照 handler 的规划（代码）处理数据，可以为每道工序指定不同的工人



## 2. EventLoop

事件循环对象
