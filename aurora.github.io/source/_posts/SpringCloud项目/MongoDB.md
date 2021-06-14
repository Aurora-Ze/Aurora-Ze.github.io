---
title: MongoDB使用
categories: tensquare
---

##### 1. 用法

在 `pom.xml` 中添加依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

在 `application.yml` 文件中添加配置（spring内）

```yaml
data:
    mongodb:
      database: commentdb
      host: 192.168.13.128
```



##### 2. 什么是MongoDB

**MongoDB**是一个基于分布式文件存储的数据库，是一个介于**关系型数据库**和**非关系型数据库**之间的产品。

它支持的数据结构非常松散，是类似于JSON格式的BSON。

它是由**C++编写**的

##### 2. MongoDB的体系结构

主要由**文档、集合、数据库**三部分组成

|      MongoDB      | 关系型数据库Mysql |
| :---------------: | :---------------: |
| 数据库(databases) | 数据库(databases) |
| 集合(collections) |     表(table)     |
|  文档(document)   |      行(row)      |

MongoDB的一个**集合**中存储了多条**文档**，文档是由多个**key-value**构成的

每条文档默认有个id

