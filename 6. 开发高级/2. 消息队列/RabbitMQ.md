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
2. message：消息体
3. exchange：交换机（接收生产者生产的消息并路由给队列）
4. queue：队列（用来保存消息直到发送给消费者。它是消息的容器，也是消息的终点。一个消息可投入一个或多个队列。消息一直在队列里面，等待消费者连接到这个队列将其取走）
5. consumer：消息消费者（从消息队列中获取消息）
6. Routing Key（路由键是供交换机查看并根据键来决定如何分发消息到列队的一个键，路由键可以说是消息的目的地址）
7. Binding（绑定，用于消息队列和交换器之间的关联。一个绑定就是基于路由键将交换器和消息队列连接起来的路由规则，所以可以将交换器理解成一个由绑定构成的路由表）
8. Connection（连接RabbitMQ和应用服务器的TCP连接）
9. Channel（信道，多路复用连接中的一条独立的双向数据流通道。信道是建立在真实的TCP连接内地虚拟连接，AMQP 命令都是通过信道发出去的，不管是发布消息、订阅队列还是接收消息，这些动作都是通过信道完成。因为对于操作系统来说建立和销毁 TCP 都是非常昂贵的开销，所以引入了信道的概念，以复用一条 TCP 连接）
10. Virtual Host（虚拟主机，表示一批交换器、消息队列和相关对象。虚拟主机是共享相同的身份认证和加密环境的独立服务器域。每个 vhost 本质上就是一个 mini 版的 RabbitMQ 服务器，拥有自己的队列、交换器、绑定和权限机制。vhost 是 AMQP 概念的基础，必须在连接时指定）

![rabbitMQ](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/rabbitMQ.5q7jk4f845.webp)



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



## 4. 数据隔离

可以在web控制台创建虚拟主机并设置其所属用户。在发送时可以选择虚拟主机进行数据隔离。



## 5. Java客户端

Spring有集成的rabbitMQ：[Maven](https://mvnrepository.com/artifact/com.rabbitmq/amqp-client)

在使用前需要先加载`spring-boot-starter-amqp`

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```



配置服务端参数：

```yaml
spring:
  rabbitmq:
    host: 121.40.46.26 # 服务端IP地址
    port: 5672 # 服务端端口号
    virtual-host: /test # 虚拟主机名
    username: test # 用户名
    password: 123 # 密码
```



### 5.1 简单演示

```java 
package com.chr.rabbitmq;

import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @Author: 程浩然
 * @Create: 2025/2/17 - 14:33
 * @Description: 生产者
 */
@RestController
@Slf4j
public class publisher {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    @RequestMapping("/publisher")
    public String publisher() {
        String queue = "queue_1";
        String msg = "消息";
        rabbitTemplate.convertAndSend(queue, msg);
        log.info("发送到消息队列中的信息为: " + msg);
        return "已读取";
    }
}

```

```java
package com.chr.rabbitmq;

import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.web.bind.annotation.RestController;

/**
 * @Author: 程浩然
 * @Create: 2025/2/17 - 14:13
 * @Description: 消费者
 */
@RestController
@Slf4j
public class consumer {
    @RabbitListener(queues = "queue_1")
    public void consumer(String msg) {
        log.info("读取到的消息队列中信息为: " + msg);
    }
}

```

> 在方法上加上`@RabbitListener(queues = "queue_1")`注解，里面指定队列名，如果队列中存在内容，就会自动执行方法。队列中的值会传递给参数。



### 5.2 work模型

1. 当队列中有一个消息，这个消息只能被消费一次。如果绑定有两个消费者，那么只能被一个消费者消费。
2. 当队列中有多个消息，绑定多个消费者，每个消费者消费的消息是平均的（类似轮询）。



这种情况下就会出现一个问题：

> 无论消费者消费水平如何（即处理消息速度快慢如何）每个消费者消费消息都是平均的，这就会导致需要等待那个处理慢的消费者。

为了解决这个问题，我们需要修改`spring.rabbitmq.simple.prefetch`的值为1。其值为预期值，即每次只能获取1个消息进行处理。

修改这个参数后可以测试出处理速度快的消费者会处理更多的消息，从而不会出现等待消费能力差的消费者。



测试代码：

```java
// 生产者
package com.chr.rabbitmq.work;

import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @Author: 程浩然
 * @Create: 2025/2/17 - 14:33
 * @Description: 生产者
 */
@RestController("work_publisher")
@Slf4j
public class publisher {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    @RequestMapping("/publisher2")
    public String work_publisher() {
        String queue = "queue_1";
        String msg = "消息__";
        for (int i = 1; i <= 50; i++) {
            rabbitTemplate.convertAndSend(queue, msg + i);
            log.info("已发送 {} 条消息", i);
        }
        return "已读取";
    }
}
```

```java
// 消费者1
package com.chr.rabbitmq.work;

import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.web.bind.annotation.RestController;

/**
 * @Author: 程浩然
 * @Create: 2025/2/17 - 14:13
 * @Description: 消费者
 */
@RestController
@Slf4j
public class consumer1 {
    @RabbitListener(queues = "queue_1")
    public void consumer1(String msg) throws InterruptedException {
        log.info("读取到的消息队列中信息为: " + msg);
        Thread.sleep(20);
    }
}
```

```java
// 消费者2
package com.chr.rabbitmq.work;

import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.web.bind.annotation.RestController;

/**
 * @Author: 程浩然
 * @Create: 2025/2/17 - 14:13
 * @Description: 消费者
 */
@RestController
@Slf4j
public class consumer2 {
    @RabbitListener(queues = "queue_1")
    public void consumer2(String msg) throws InterruptedException {
        log.info("读取到的消息队列中信息为:     " + msg);
        Thread.sleep(200);
    }
}
```





### 5.3 Fanout交换机

<font color="red">Fanout交换机即广播交换机，会将消费生产者发送给交换机链接的所有队列。又叫做广播模式。</font>

交换机的类型在web端控制界面设置。在第6节会说明声明交换机和队列的方式。

```java
package com.chr.rabbitmq.fanout;

import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @Author: 程浩然
 * @Create: 2025/2/17 - 14:33
 * @Description: 生产者
 */
@RestController("fanout_publisher")
@Slf4j
public class publisher {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    @RequestMapping("/publisher3")
    public String publisher() {
        String exchanges = "test.fanout";
        String msg = "消息";
        rabbitTemplate.convertAndSend(exchanges, "", msg);
        log.info("发送到消息队列中的信息为: " + msg);
        return "已发送";
    }
}
```

> `rabbitTemplate.convertAndSend`方法可以有三个参数，第一个交换机，第二个路由键，第三个消息



### 5.4 Direct 交换机

<font color="red">direct交换机即为分组交换机，会给一部分队列发送消息，称为定向路由。</font>

在将队列与交换机连接时需要指定`Routing key`（路由键），这个值是队列的值。

当生产者发送消息到交换机时，交换机会根据`Routing key`来发送给正确的队列。

```java
package com.chr.rabbitmq.direct;

import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @Author: 程浩然
 * @Create: 2025/2/17 - 14:33
 * @Description: 生产者
 */
@RestController("direct_publisher")
@Slf4j
public class publisher {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    @RequestMapping("/publisher4")
    public String publisher() {
        String exchanges = "test.direct";
        String msg = "消息";
        rabbitTemplate.convertAndSend(exchanges, "3", msg);
        log.info("direct 发送到消息队列中的信息为: " + msg);
        return "已读取";
    }
}
```

> 此时`convertAndSend`方法的三个参数都有值，第二个参数就是routing key



### 5.5 <font color="red">Topic 交换机</font>

Topic 交换机和Driect 交换机功能类似。区别在于Topic 交换机可以实现多个单词，中间用`.`分开

单词还支持通配符：<font color="red">#表示0个或多个单词，* 表示一个单词</font>

Topic 交换机可以实现前两个交换机的功能

> <font color="red">多个空格和Null算是一个单词</font>

```java
package com.chr.rabbitmq.Topic;

import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @Author: 程浩然
 * @Create: 2025/2/17 - 14:33
 * @Description: 生产者
 */
@RestController("topic_publisher")
@Slf4j
public class publisher {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    @RequestMapping("/publisher5")
    public String publisher() {
        String exchanges = "test.topic";
        String msg = "消息";
        rabbitTemplate.convertAndSend(exchanges, "a.a.  .news", msg);
        log.info("direct 发送到消息队列中的信息为: " + msg);
        return "已读取";
    }
}
```





## 6. 声明交换机和队列的方式

### 1.  手动在web端声明（不推荐）

### 2.Java代码声明（推荐）

#### 2.1 写配置类（不推荐）

```java
package com.chr.rabbitmq.config;

import org.springframework.amqp.core.Binding;
import org.springframework.amqp.core.BindingBuilder;
import org.springframework.amqp.core.FanoutExchange;
import org.springframework.amqp.core.Queue;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @Author: 程浩然
 * @Create: 2025/2/17 - 21:09
 * @Description: fanout交换机和队列配置
 */
@Configuration
public class FanoutConfiguration {
    @Bean
    public FanoutExchange fanoutExchange() {
//        return ExchangeBuilder.fanoutExchange("fanout2").build();
        return new FanoutExchange("fanout2");
    }

    @Bean
    public Queue fanoutQueue() {
//        return QueueBuilder.durable("name").build(); // 持久化的
//        return QueueBuilder.nonDurable("name").build(); // 一次性的
        return new Queue("name");
    }

    @Bean
    public Binding fanoutBinding(Queue fanoutQueue, FanoutExchange fanoutExchange) {
        return BindingBuilder.bind(fanoutQueue).to(fanoutExchange);
    }
}
```



#### 2.2 写注解

```java
@RabbitListener(bindings = @QueueBinding(
            value = @Queue(value = "queue_name", durable = "true"),
            exchange = @Exchange(value = "exchange_name", type = ExchangeTypes.DIRECT),
            key = {"red", "blue"}
    ))
```



## 7. 消息转换器









## 总结

#### 流程：

1. 生产者生成消息，通过`rabbitTemplate.convertAndSend(exchanges, "routingKey", msg);`方式发送给交换机。
2. 交换机有四种类型，根据不同的类型和参数(`routingKey`)将消息发送给相应的队列。
3. 队列会有 0 个或多个消费者绑定，需要通过配置文件设置<font color="red">预取值</font>实现加快处理速度。

在微服务项目中，每个交换机对应一个模块，每个队列对应一个具体业务



#### 交换机：

- **Headers**：基于消息头（headers）中的键值对进行路由。交换机会检查消息头中的键值对是否与绑定时的键值对匹配。匹配规则可以是全部匹配（all）或任意匹配（any）。
- **Direct**：基于路由键（routing key）进行精确匹配。如果路由键与绑定键完全相同，消息就会被路由到相应的队列。
- **Topic**：基于路由键进行模式匹配。路由键可以使用通配符（如`*`匹配一个单词，`#`匹配零个或多个单词），从而实现更灵活的路由。
- **Fanout**：不基于路由键进行路由，而是将消息广播到所有绑定的队列，无论路由键是什么。



#### 申明交换机和队列的方式：

申明交换机和队列的方式是在消费者上配置，生产者无需关心交换机和队列。

配置类声明总结：

1. 每一个交换机和队列的组合都需要写一个交换机，一个队列，一个连接。
2. 交换机可以重复。

注解配置总结：

1. 一次只能配置一个交换机和一个队列和多个Routing key
