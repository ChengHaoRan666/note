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
