---
title: RabbitMQ
categories: tensquare
---

#### 1. 添加依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

#### 2. 添加配置

```yaml
# 要添加在spring字段内
rabbitmq:
  host: 192.168.13.128 
```

#### 3. 具体使用

```java
// 创建管理器
RabbitAdmin rabbitAdmin = new RabbitAdmin(rabbitTemplate.getConnectionFactory());
// 声明交换机
DirectExchange exchange = new DirectExchange("article_subscribe");
rabbitAdmin.declareExchange(exchange);
// 创建消息队列
Queue queue = new Queue("article_subscribe" +  userId, true);
// 绑定交换机与队列
Binding binding = BindingBuilder.bind(queue).to(exchange).with(authorId);
```