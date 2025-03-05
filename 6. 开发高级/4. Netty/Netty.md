导入Maven依赖

```xml
<!--Netty 依赖-->
<dependency>
    <groupId>io.netty</groupId>
    <artifactId>netty-all</artifactId>
    <version>4.1.42.Final</version>
</dependency>
```



实现单对单聊天

```java
package com.chr.netty;

import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.Channel;
import io.netty.channel.EventLoopGroup;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;

/**
 * 负责启动
 */
@Slf4j
@Component
public class SingleChat {
    public static void start() {
        EventLoopGroup bossGroup = new NioEventLoopGroup(1);
        // 创建子线程组，专门负责IO任务的处理
        EventLoopGroup workGroup = new NioEventLoopGroup();
        try {
            ServerBootstrap bootstrap = new ServerBootstrap();
            bootstrap.group(bossGroup, workGroup);
            bootstrap.channel(NioServerSocketChannel.class);
            bootstrap.childHandler(new WebSocketChannelInitializer());
            log.info("Chat服务已启动....");
            Channel ch = bootstrap.bind(9090).sync().channel();
            ch.closeFuture().sync();


        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //退出程序
            bossGroup.shutdownGracefully();
            workGroup.shutdownGracefully();
        }
    }
}
```

```java
package com.chr.netty;


import io.netty.channel.ChannelInitializer;
import io.netty.channel.socket.SocketChannel;
import io.netty.handler.codec.http.HttpObjectAggregator;
import io.netty.handler.codec.http.HttpServerCodec;
import io.netty.handler.stream.ChunkedWriteHandler;


/**
 * @Author: 程浩然
 * @Create: 2025/3/5 - 10:42
 * @Description: 初始化服务器管道
 */
public class WebSocketChannelInitializer extends ChannelInitializer<SocketChannel> {
    @Override
    protected void initChannel(SocketChannel ch) throws Exception {
        ch.pipeline().addLast("http-codec", new HttpServerCodec());//设置解码器
        ch.pipeline().addLast("aggregator", new HttpObjectAggregator(65536));//聚合器，使用websocket会用到
        ch.pipeline().addLast("http-chunked", new ChunkedWriteHandler());//用于大数据的分区传输
        ch.pipeline().addLast(new ChatEngine());
    }
}
```

```java
package com.chr.netty;

import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import io.netty.channel.Channel;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.SimpleChannelInboundHandler;
import io.netty.handler.codec.http.FullHttpRequest;
import io.netty.handler.codec.http.QueryStringDecoder;
import io.netty.handler.codec.http.websocketx.*;
import io.netty.util.AttributeKey;
import lombok.extern.slf4j.Slf4j;

import java.util.List;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

/**
 * @Author: 程浩然
 * @Create: 2025/3/5 - 10:41
 * @Description: 处理连接服务器的消息
 */
@Slf4j
public class ChatEngine extends SimpleChannelInboundHandler<Object> {
    private static final AttributeKey<String> USER_ID_KEY = AttributeKey.valueOf("userId");
    private static final Map<String, Channel> userChannelMap = new ConcurrentHashMap<>();
    private WebSocketServerHandshaker handshaker;

    /**
     * 当一个客户端与服务器建立 WebSocket 连接并且通道（Channel）激活时（即连接成功后），这个方法会被自动调用
     *
     * @param ctx
     * @throws Exception
     */
    @Override
    public void channelActive(ChannelHandlerContext ctx) throws Exception {
        log.info("{} 申请连接建立新的 WebSocket", ctx.channel().remoteAddress());
    }


    /**
     * 处理用户下线
     *
     * @param ctx
     * @throws Exception
     */
    @Override
    public void channelInactive(ChannelHandlerContext ctx) throws Exception {
        String userId = ctx.channel().attr(USER_ID_KEY).get();
        if (userId != null) {
            userChannelMap.remove(userId); // 移除用户
            log.info("用户 {} 下线，当前在线人数: {}", userId, userChannelMap.size());
        }
    }


    /**
     * 用于处理 Netty 服务器接收到的消息<br>
     * 如果接收到的是 HTTP 请求（FullHttpRequest），则处理 WebSocket 握手请求，即 建立 WebSocket 连接<br>
     * 如果接收到的是 WebSocket 消息（WebSocketFrame），则处理 WebSocket 消息传输，即 解析、转发消息<br>
     *
     * @param ctx
     * @param msg
     * @throws Exception
     */
    @Override
    protected void channelRead0(ChannelHandlerContext ctx, Object msg) throws Exception {
        if (msg instanceof FullHttpRequest) {
            //以http请求形式接入，但是走的是websocket
            handleHttpRequest(ctx, (FullHttpRequest) msg);
        } else if (msg instanceof WebSocketFrame) {
            //处理websocket客户端的消息
            handlerWebSocketFrame(ctx, (WebSocketFrame) msg);
        }
    }


    /**
     * 首次创建webSocket，发送的http请求，申请建立连接
     */
    private void handleHttpRequest(ChannelHandlerContext ctx, FullHttpRequest req) {
        QueryStringDecoder decoder = new QueryStringDecoder(req.uri());
        Map<String, List<String>> parameters = decoder.parameters();

        // 从请求参数中获取 userId
        String userId = parameters.containsKey("userId") ? parameters.get("userId").get(0) : null;

        if (userId == null || userId.isEmpty()) {
            log.error("未能获取到 userId，WebSocket 连接失败");
            ctx.close();
            return;
        }

        // 存入 Netty Channel 的 AttributeMap
        ctx.channel().attr(USER_ID_KEY).set(userId);
        userChannelMap.put(userId, ctx.channel()); // **在这里绑定用户 ID 和 Channel**
        log.info("WebSocket 连接建立，用户ID: {}，当前在线人数: {}", userId, userChannelMap.size());

        // **执行 WebSocket 握手**
        WebSocketServerHandshakerFactory wsFactory = new WebSocketServerHandshakerFactory(req.uri(), null, false);
        handshaker = wsFactory.newHandshaker(req);
        if (handshaker == null) {
            WebSocketServerHandshakerFactory.sendUnsupportedVersionResponse(ctx.channel());
        } else {
            handshaker.handshake(ctx.channel(), req);
        }
    }


    /**
     * 已经建立websocket连接，处理发送的消息
     */
    private void handlerWebSocketFrame(ChannelHandlerContext ctx, WebSocketFrame frame) {
        if (frame instanceof CloseWebSocketFrame) {
            handshaker.close(ctx.channel(), (CloseWebSocketFrame) frame.retain());
            return;
        }
        if (frame instanceof PingWebSocketFrame) {
            ctx.channel().write(new PongWebSocketFrame(frame.content().retain()));
            return;
        }

        if (frame instanceof TextWebSocketFrame) {
            String text = ((TextWebSocketFrame) frame).text();
            log.info("收到消息: \n{}", text);

            try {
                // 解析 JSON 消息
                ObjectMapper objectMapper = new ObjectMapper();
                JsonNode jsonNode = objectMapper.readTree(text);
                String toUserId = jsonNode.has("to") ? jsonNode.get("to").asText() : null; // 目标用户
                String message = jsonNode.get("message").asText(); // 消息内容
                String fromUserId = ctx.channel().attr(USER_ID_KEY).get(); // 发送者 ID
                System.out.println("发送者：" + fromUserId + "接收者: " + toUserId + "消息: " + message);
                if (toUserId != null && !toUserId.isEmpty()) {
                    // **🔹 单聊逻辑**
                    if (userChannelMap.containsKey(toUserId)) {
                        Channel targetChannel = userChannelMap.get(toUserId);
                        targetChannel.writeAndFlush(new TextWebSocketFrame("【" + fromUserId + "】私信你：" + message));
                    } else {
                        ctx.channel().writeAndFlush(new TextWebSocketFrame("用户 " + toUserId + " 不在线"));
                    }
                } else {
                    System.out.println("错误：群聊");
                }
            } catch (Exception e) {
                log.error("解析 WebSocket 消息失败", e);
                ctx.channel().writeAndFlush(new TextWebSocketFrame("消息格式错误！"));
            }
        }
    }
}
```





SpringBoot设置：

```java
package com.chr.netty;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@Slf4j
@SpringBootApplication
public class NettyApplication {
    @Autowired
    private SingleChat singleChat;

    public static void main(String[] args) {
        SpringApplication.run(NettyApplication.class, args);
        log.info("SpringBoot启动...");
        SingleChat.start();
        log.info("netty启动...");
    }
}
```



前端代码：

```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WebSocket 单聊</title>
    <style>
        body { font-family: Arial, sans-serif; }
        #chat-box { width: 100%; height: 300px; border: 1px solid #ccc; overflow-y: scroll; padding: 10px; }
        #message-input { width: 80%; padding: 5px; }
        #send-btn { padding: 5px; }
    </style>
</head>
<body>
<h2>WebSocket 单聊</h2>
<label>你的用户ID: <input type="text" id="userId" /></label>
<button onclick="connectWebSocket()">连接</button>
<button onclick="disconnectWebSocket()">断开连接</button>
<br><br>
<div id="chat-box"></div>
<br>
<label>接收者ID: <input type="text" id="toUserId" /></label>
<br>
<input type="text" id="message-input" placeholder="输入消息..." />
<button id="send-btn" onclick="sendMessage()">发送</button>
<script>
    let ws;
    // 连接
    function connectWebSocket() {
        const userId = document.getElementById('userId').value.trim();
        if (!userId) {
            alert('请输入用户ID');
            return;
        }
        ws = new WebSocket(`ws://localhost:9090/websocket?userId=${userId}`);
        ws.onopen = () => appendMessage('✅ 连接成功！');
        ws.onmessage = event => appendMessage(event.data);
        ws.onclose = () => appendMessage('❌ 连接已关闭');
        ws.onerror = error => appendMessage('⚠️ 连接错误: ' + error);
    }

    // 发送消息
    function sendMessage() {
        if (!ws || ws.readyState !== WebSocket.OPEN) {
            alert('请先连接 WebSocket');
            return;
        }
        const toUserId = document.getElementById('toUserId').value.trim();
        const message = document.getElementById('message-input').value.trim();
        if (!toUserId || !message) {
            alert('请输入接收者ID和消息内容');
            return;
        }
        const msgObj = { to: toUserId, message: message };
        ws.send(JSON.stringify(msgObj));
        appendMessage(`📤 你 -> ${toUserId}: ${message}`);
    }

    // 关闭连接
    function disconnectWebSocket() {
        if (ws) {
            ws.close();  // 关闭 WebSocket 连接
            appendMessage('🚪 你已退出聊天');
        }
    }

    // 输出语句
    function appendMessage(text) {
        const chatBox = document.getElementById('chat-box');
        chatBox.innerHTML += `<p>${text}</p>`;
        chatBox.scrollTop = chatBox.scrollHeight;
    }

</script>
</body>
</html>
```

