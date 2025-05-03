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

### EventLoop - 事件循环对象

`EventLoop `本质是一个单线程执行器（同时维护了一个 `Selector`），里面有 `run `方法处理 `Channel `上源源不断的 io 事件。

它的继承关系比较复杂

- 一条线是继承自 `j.u.c.ScheduledExecutorService` 因此包含了线程池中所有的方法
- 另一条线是继承自 `netty` 自己的 `OrderedEventExecutor`，
  - 提供了 `boolean inEventLoop(Thread thread)`方法判断一个线程是否属于此 `EventLoop`
  - 提供了 `parent `方法来看看自己属于哪个 `EventLoopGroup`



### EventLoopGroup - 事件循环组

`EventLoopGroup `是一组 `EventLoop`，`Channel `一般会调用 `EventLoopGroup `的 `register `方法来绑定其中一个 `EventLoop`，后续这个 `Channel `上的 io 事件都由此 `EventLoop` 来处理（保证了 io 事件处理时的线程安全）

- 继承自 `netty `自己的 `EventExecutorGroup`
  - 实现了 `Iterable `接口提供遍历 `EventLoop `的能力
  - 另有 `next `方法获取集合中下一个 `EventLoop`



```java
// 内部创建了两个 EventLoop, 每个 EventLoop 维护一个线程
DefaultEventLoopGroup group = new DefaultEventLoopGroup(2);
System.out.println(group.next());
System.out.println(group.next());
System.out.println(group.next());
```

输出：

```
io.netty.channel.DefaultEventLoop@41488b16
io.netty.channel.DefaultEventLoop@a8ef162
io.netty.channel.DefaultEventLoop@41488b16
```



也可以使用 for 循环

```java
DefaultEventLoopGroup group = new DefaultEventLoopGroup(2);
for (EventExecutor eventLoop : group) {
    System.out.println(eventLoop);
}
```

输出：

```
io.netty.channel.DefaultEventLoop@41488b16
io.netty.channel.DefaultEventLoop@a8ef162
```



> `EventLoopGroup`有两个实现，:`NioEventLoopGroup`，`DefaultEventLoopGroup`
>
> `NioEventLoopGroup`：可以处理 io事件，普通任务，定时任务
>
> `DefaultEventLoopGroup`：可以处理普通任务，定时任务
>
> 
>
> 传入的参数是设置的线程数，不传默认电脑核数 * 2



#### 执行普通任务

```java
EventLoopGroup loopGroup = new NioEventLoopGroup(2);
loopGroup.next().submit(() -> {
    try {
        Thread.sleep(1000);
    } catch (InterruptedException e) {
        throw new RuntimeException(e);
    }
    log.debug("ok");
});
log.debug("main");
```

- 会异步执行普通任务

```
2025-05-03 19:22:10 [main] - main
2025-05-03 19:22:11 [nioEventLoopGroup-2-1] - ok
```



#### 执行定时任务

```java
EventLoopGroup loopGroup = new NioEventLoopGroup(2);
log.debug("开始");
loopGroup.next().scheduleAtFixedRate(
    () -> log.debug("ok"), 5,1, TimeUnit.SECONDS);
```

- 第一个参数：`Runnable`，要执行的任务
- 第二个参数：初始延迟，任务会在 延迟 x 秒 后首次执行
- 第三个参数：执行间隔，任务在首次执行后，每 x 秒 再次执行一次
- 第四个参数：时间单位

```
2025-05-03 19:29:35 [main] - 开始
2025-05-03 19:29:40 [nioEventLoopGroup-2-1] - ok
2025-05-03 19:29:41 [nioEventLoopGroup-2-1] - ok
2025-05-03 19:29:42 [nioEventLoopGroup-2-1] - ok
2025-05-03 19:29:43 [nioEventLoopGroup-2-1] - ok
2025-05-03 19:29:44 [nioEventLoopGroup-2-1] - ok
```



#### 处理IO事件

服务器端：

```java
new ServerBootstrap()
        .group(new NioEventLoopGroup())
        .channel(NioServerSocketChannel.class)
        .childHandler(new ChannelInitializer<NioSocketChannel>() {
            @Override
            protected void initChannel(NioSocketChannel nioSocketChannel) throws Exception {
                nioSocketChannel.pipeline().addLast(new ChannelInboundHandlerAdapter() {
                    // 关注读事件
                    @Override
                    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
                        ByteBuf buf = (ByteBuf) msg;
                        log.debug(buf.toString(Charset.defaultCharset()));
                    }
                });
            }
        }).bind(8080);
```

客户端：

```java
// 创建启动类
Channel channel = new Bootstrap()
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
        .channel();
log.debug("客户端启动");
System.out.println(channel);
```

发送结果（断点调试发送）：

```
2025-05-03 19:41:59 [nioEventLoopGroup-2-3] - 1
2025-05-03 19:42:05 [nioEventLoopGroup-2-3] - 2
2025-05-03 19:42:08 [nioEventLoopGroup-2-3] - 3
2025-05-03 19:42:12 [nioEventLoopGroup-2-3] - 啊
2025-05-03 19:43:32 [nioEventLoopGroup-2-4] - abcd
```

<font color="red">一个客户端连接，他的事件会被一个`EventLoop`一直处理</font>

![0042](https://ChengHaoRan666.github.io/picx-images-hosting/Netty/0042.83a90y049w.webp)

​	



##### 分工细化：

在处理IO事件时，分工可以再细化：

在`.group(new NioEventLoopGroup())`时向里面传入两个参数

```java
// boss 负责 ServerSocketChannel 上的 accept 事件
// worker 负责 SocketChannel 上的 read write 事件 
.group( new NioEventLoopGroup(), new NioEventLoopGroup())
```



##### 更进一步：

在处理IO事件时，如果有一个任务很耗时，那么就会耽误`EventLoop`的其他事件处理，这时候可以把这个事件由其他`Group`处理，具体方法就是在`addLast`时指定给哪个`EventLoopGroup`处理，第二个参数时名字

```java
DefaultEventLoopGroup defaultEventLoopGroup = new DefaultEventLoopGroup();

new ServerBootstrap()
    // boss 负责 ServerSocketChannel 上的 accept 事件
    // worker 负责 SocketChannel 上的 read write 事件
    .group(new NioEventLoopGroup(), new NioEventLoopGroup(2))
    .channel(NioServerSocketChannel.class)
    .childHandler(new ChannelInitializer<NioSocketChannel>() {
        @Override
        protected void initChannel(NioSocketChannel nioSocketChannel) throws Exception {
            nioSocketChannel.pipeline().addLast(new ChannelInboundHandlerAdapter() {
                // 关注读事件
                @Override
                public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
                    ByteBuf buf = (ByteBuf) msg;
                    log.debug(buf.toString(Charset.defaultCharset()));
                    // 将消息传给下个handle处理
                    ctx.fireChannelRead(msg);
                }
            }).addLast(defaultEventLoopGroup, "defaultGroup", new ChannelInboundHandlerAdapter() {
                // 关注读事件
                @Override
                public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
                    ByteBuf buf = (ByteBuf) msg;
                    log.debug(buf.toString(Charset.defaultCharset()));
                }
            });
        }
    }).bind(8080);
```

```
2025-05-03 20:15:08 [nioEventLoopGroup-4-1] - 1
2025-05-03 20:15:08 [defaultEventLoopGroup-2-1] - 1
2025-05-03 20:15:31 [nioEventLoopGroup-4-2] - 2
2025-05-03 20:15:31 [defaultEventLoopGroup-2-2] - 2
2025-05-03 20:15:52 [nioEventLoopGroup-4-1] - 3
2025-05-03 20:15:52 [defaultEventLoopGroup-2-3] - 3
```

![0041](https://ChengHaoRan666.github.io/picx-images-hosting/Netty/0041.7lk7ce1md8.webp)



##### handler 执行中如何换人？

关键代码 `io.netty.channel.AbstractChannelHandlerContext#invokeChannelRead()`

```java
static void invokeChannelRead(final AbstractChannelHandlerContext next, Object msg) {
    final Object m = next.pipeline.touch(ObjectUtil.checkNotNull(msg, "msg"), next);
    // 下一个 handler 的事件循环是否与当前的事件循环是同一个线程
    EventExecutor executor = next.executor();
    
    // 是，直接调用
    if (executor.inEventLoop()) {
        next.invokeChannelRead(m);
    } 
    // 不是，将要执行的代码作为任务提交给下一个事件循环处理（换人）
    else {
        executor.execute(new Runnable() {
            @Override
            public void run() {
                next.invokeChannelRead(m);
            }
        });
    }
}
```



- 如果两个 handler 绑定的是同一个线程，那么就直接调用
- 否则，把要调用的代码封装为一个任务对象，由下一个 handler 的线程来调用
