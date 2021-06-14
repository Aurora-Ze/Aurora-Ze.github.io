---
title: MyBatisPlus使用
categories: tensquare
---

##### 1. 在 `pom.xml` 中添加依赖

```xml
<dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatisplus-spring-boot-starter</artifactId>
            <version>${mybatisplus-spring-boot-starter.version}</version>
</dependency>
<dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus</artifactId>
            <version>${mybatisplus.version}</version>
</dependency>
```

##### 2. 在 `application.yml` 文件中添加配置

```yaml
mybatis-plus:
  typeAliasesPackage: com.tensquare.article.pojo
  global-config:
    id-type: 1  
    db-column-underline: false
    refresh-mapper: true
    configuration:
      map-underscore-to-camel-case: true
      cache-enabled: true 
      lazyLoadingEnabled: true 
      multipleResultSetsEnabled: true 
      log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

说明

|           属性            |                    描述                     |
| :-----------------------: | :-----------------------------------------: |
|    typeAliasesPackage     | 实体类所在包，多个package用逗号或者分号分隔 |
|          id-type          |    0表示数据库的自增ID，1表示用户输入ID     |
|       cache-enabled       |                缓存是否开启                 |
|    lazyLoadingEnabled     |               懒加载是否开启                |
| multipleResultSetsEnabled |              延时加载是否开启               |
|         log-impl          |            打印SQL语句，用做调试            |

