---
title: Eureka使用
categories: tensquare
---

功能：统一管理模块

### 使用步骤

#### 1. 在父工程的`pom.xml`文件下添加依赖

另起一块

```XML
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>Greenwich.SR1</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

-------------------------

#### 2. 创建Eureka的微服务模块

右键 - > new module - > 选择maven工程进行创建

然后在Eureka模块中添加依赖：

```XML
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
    </dependency>
</dependencies>
```

在resource目录下添加`application.yml`文件

```YAML
server:
  port: 6868
eureka:
  client:
    register-with-eureka: false #是否将自己注册到eureka中
    fetch-registry: false #是否从eureka中获取信息
    service-url:
      defaultZone: http://127.0.0.1:${server.port}/eureka/
```

编写启动类

```Java
@SpringBootApplication
@EnableEurekaServer
public class EurekaApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaApplication.class, args);
    }
}
```

-----------------------------------------

#### 3. 将其他模块注册到Eureka中

在pom.xml中添加Eureka依赖

```XML
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

修改配置文件，使用Eureka

```yaml
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:6868/eureka/
  instance:
    prefer-ip-address: true
```

然后在启动类中添加注解：@EnableEurekaClient

------------------------

