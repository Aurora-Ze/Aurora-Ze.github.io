# 一、持久化方式

持久化主要有两种方式，RDB用来存数据，AOF用来存操作。

下面我们详细介绍一下这两种方式。

# 二、RDB

### 2.1简单了解

#### 定义

> **RDB**就是把**数据库状态**保存为RDB文件，放入磁盘里，避免数据意外丢失
>
> 这里的**数据库状态**指的是**非空数据库和里面保存的键值对**

#### 过程

1. 创建RDB文件

   通过`SAVE`和`BGSAVE`命令来实现，底层都是调用`rdbSave()`方法

   前者阻塞服务器，后者开一个子进程(fork)去执行

2. 加载RDB文件

   Redis在启动时会检测是否存在`RDB`文件，如果存在，则自动加载。

### 2.2自动保存

#### ①配置参数

用户显式地去调用上述命令肯定不现实，所以Redis提供了配置参数。

用户可以手动配置参数，也可以使用默认的；Redis根据配置，每隔一段时间调用`BGSAVE`命令。

```bash
# 默认配置
save 900 1
save 300 10
save 60 10000

# save 900 1 表示900秒内执行了1次修改(写入,更新,删除)操作，就会调用BGSAVE;
```

#### ②参数保存

参数：

- seconds，时间范围
- changes，修改次数

redis中有`redisServer`结构体，配置的参数会保存其中的`saveparam`结构体。

```c
# redis 服务器结构体
struct redisServer {
    //...
    struct saveparam *saveparams;
}

# 保存变量的结构体
struct saveparam {
    time_t seconds;
    int changes;
}

# 
# 
```

#### ③检测

> 光有配置还不够，Redis需要知道距离上次持久化的时间，以及已经执行多少次修改了。
>
> 所以在redis结构体中保存变量dirty和lastsave

```c
struct redisServer {
    // ...
    long long dirty;
    time_t lastsave;
    struct saveparam *saveparams;
}
```

redis定期（默认每隔100ms）执行一次检查，判断当前时间和修改次数是否满足save的参数配置

**说明**

下面还要介绍另一种持久化方式`AOF`，因为`AOF`的频率比`RDB`高，所以它优先级也高；

一般只有没用`AOF`的时候，才会用`RDB`来还原数据库状态。

### 2.3结构分析

RDB文件格式如图所示：

注意，是二进制存储的！

![image-20210510131452466](http://raw.githubusercontent.com/Aurora-Ze/pictures/master/img/20210510131452.png)

- REDIS：“REDIS”，5个字节
- db_version：RDB版本号，4字节
- databases：数据库及键值对数据，长度不确定
- EOF：标志位，说明正文部分结束，1字节
- check_sum：校验和，8字节

重点在于databases部分，因为这里存了真正的数据。

我们知道redis有多个数据库，所以databases也是按数据库的顺序来存（非空）。

# 三、AOF

### 简单定义

记录每次写操作，持久化到磁盘中。

------



