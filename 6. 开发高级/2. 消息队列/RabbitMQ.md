## 0. RabbitMQ安装

1. `docker pull rabbitmq:3.8-management`安装

2. `docker run -d --name rabbitmq \`
    `-e RABBITMQ_DEFAULT_USER=admin \ `
    `-e RABBITMQ_DEFAULT_PASS=admin \ `
    `-p 15672:15672 \`
    `-p 5672:5672 \`
    `-p 61613:61613 \`
    `-p 1883:1883 \`
    `rabbitmq:3.8-management `

> 开放端口作用：
>
> 15672端口：web监控插件端口
>
> 5672：默认端口，AMQP协议的消息传递
>
> 61613：STOMP协议的消息传递
>
> 1883：MQTT协议的消息传递



## 1. 初始RabbitMQ

#### 1. 同步调用

优点：简单，直接

缺点：代码耦合，拓展性差，性能差，级联失败



#### 2. 异步调用

异步调用使用==消息通知==的方式，一般有三种角色：

1. 消息发送者：原本的服务申请方
2. 消息代理：管理，暂存，转发消息
3. 消息接收者：原本的服务提供方

异步调用可以直接解耦，当需要跨模块调用时，可以直接使用消息队列进行发送消息，无需等待其他模块完成。

优点：耦合度低，拓展性强，性能好，故障隔离，平衡流量

缺点：不能实时得到调用结果，时效性差，依赖代理可靠性



## 2. 消息发送流程

​	rabbitMQ组成：

1. publisher：消息生产者（将消息发送给交换机）
2. message：消息
3. exchange：交换机（接收生产者生产的消息并路由给队列）
4. queue：队列（用来保存消息直到发送给消费者。它是消息的容器，也是消息的终点。一个消息可投入一个或多个队列。消息一直在队列里面，等待消费者连接到这个队列将其取走）
5. consumer：消息消费者（从消息队列中获取消息）
6. Routing Key（路由键是供交换机查看并根据键来决定如何分发消息到列队的一个键，路由键可以说是消息的目的地址）
7. Binding（绑定，用于消息队列和交换器之间的关联。一个绑定就是基于路由键将交换器和消息队列连接起来的路由规则，所以可以将交换器理解成一个由绑定构成的路由表）
8. Connection（连接RabbitMQ和应用服务器的TCP连接）
9. Channel（信道，多路复用连接中的一条独立的双向数据流通道。信道是建立在真实的TCP连接内地虚拟连接，AMQP 命令都是通过信道发出去的，不管是发布消息、订阅队列还是接收消息，这些动作都是通过信道完成。因为对于操作系统来说建立和销毁 TCP 都是非常昂贵的开销，所以引入了信道的概念，以复用一条 TCP 连接）
10. Virtual Host（虚拟主机，表示一批交换器、消息队列和相关对象。虚拟主机是共享相同的身份认证和加密环境的独立服务器域。每个 vhost 本质上就是一个 mini 版的 RabbitMQ 服务器，拥有自己的队列、交换器、绑定和权限机制。vhost 是 AMQP 概念的基础，必须在连接时指定）





## 3. 测试

### web端测试：

1. 先创建一个队列
2. 将交换机和队列进行绑定
3. 在交换机中发送消息
4. 在队列中可以查看已存在



### 代码测试：

```java
package com.chr;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.nio.charset.StandardCharsets;

/**
 * 生产者
 */
public class Send {

    private final static String QUEUE_NAME = "queue_1";

    public static void main(String[] argv) throws Exception {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("121.40.46.26");
        factory.setPort(5672);
        factory.setUsername("admin");
        factory.setPassword("admin");
        try (Connection connection = factory.newConnection();
             Channel channel = connection.createChannel()) {
            channel.queueDeclare(QUEUE_NAME, false, false, false, null);
            String message = "发送内容1";
            channel.basicPublish("", QUEUE_NAME, null, message.getBytes(StandardCharsets.UTF_8));
            System.out.println(" [x] Sent '" + message + "'");
        }
    }
}
```

```java
package com.chr;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;
import com.rabbitmq.client.DeliverCallback;
import java.nio.charset.StandardCharsets;

/**
 * 消费者
 */
public class Recv {

    private final static String QUEUE_NAME = "queue_1";

    public static void main(String[] argv) throws Exception {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("121.40.46.26");
        factory.setPort(5672);
        factory.setUsername("admin");
        factory.setPassword("admin");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();

        channel.queueDeclare(QUEUE_NAME, false, false, false, null);
        System.out.println(" [*] Waiting for messages. To exit press CTRL+C");

        DeliverCallback deliverCallback = (consumerTag, delivery) -> {
            String message = new String(delivery.getBody(), StandardCharsets.UTF_8);
            System.out.println(" [x] Received '" + message + "'");
        };
        channel.basicConsume(QUEUE_NAME, true, deliverCallback, consumerTag -> { });
    }
}
```

在生产者中创建一个队列queue_1，向队列中发送消息。在消费者中绑定队列queue_1，可以获取发送的消息。

![rabbitMQ](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/rabbitMQ.5q7jk4f845.webp)

## 4. 数据隔离

可以在web控制台创建虚拟主机并设置其所属用户。在发送时可以选择虚拟主机进行数据隔离。



## 5. Java客户端

