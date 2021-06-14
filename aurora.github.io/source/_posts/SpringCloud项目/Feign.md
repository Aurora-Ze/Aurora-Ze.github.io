---
title: Feign使用
categories: tensquare
---

### Feign

功能：用来调用**其他微服务**所提供的接口

##### 1. 在本服务的`pom.xml`中添加Feign依赖，并在启动类上添加注解

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

```java
@EnableFeignClients
public class NoticeApplication {}
```

##### 2. 在`client`包下创建其他微服务接口类

```java
@FeignClient("tensquare-article")
public interface ArticleClient {

    @RequestMapping(value = "article/{id}", method = RequestMethod.GET)
    Result findById(@PathVariable("id") String id);
}
```

接口中声明要使用的方法（必须是其他微服务实现好的）

##### 3. 注入Bean以调用接口

```java
@Autowired
private UserClient userClient;
// ...	userClient.findById("123");
```

