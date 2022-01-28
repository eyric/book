# Redis

### 一、Redis基础概念

Redis（Remote Dictionary Server ，远程字典服务)是现在最受欢迎的NoSQL数据库之一，Redis是一个使用ANSI C编写的开源、包含多种数据结构、支持网络、基于内存、可选持久性的键值对存储数据库，具有丰富的应用场景，例如：缓存系统（“热点”数据：高频读、低频写）、计数器、消息队列系统、排行榜、社交网络和实时系统。其具备如下特性：

* 基于内存运行，性能高效
* 支持分布式，理论上可以无限扩展
* key-value存储系统
* 开源的使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的AP

相比于其他数据库类型，Redis具备的特点是：

* C/S通讯模型
* 单进程单线程模型
* 丰富的数据类型
* 操作具有原子性
* 持久化
* 高并发读写
* 支持lua脚本

#### Mac环境Redis安装

* 搜索Redis安装包

```shell
(base) B000000348954H:~ liwantao$ brew search redis
==> Formulae
hiredis                   redis ✔                   redis-leveldb             redis@3.2                 redis@4.0 ✔
==> Casks
another-redis-desktop-manager               redis                                       redisinsight

```

* 安装 Redis 4.0 版本

```
brew install redis@4.0
```

* 配置 Redis configure文件：/usr/local/etc 下修改redis.config找到

> daemonize no改成yes 以守护进程的方式启动

* 配置环境变量

```
vim ~/.bash_profile
```

&#x20;     添加如下内容：

```
export PATH="/usr/local/opt/redis@4.0/bin:$PATH"
```

* 启动 Redis Server

```
brew services start redis@4.0
```

&#x20;   或者

```
redis-server /usr/local/etc/redis.conf
```

* 连接 Redis 客户端（Client）：`redis-cli -h host -p port`

```
redis-cli -h 127.0.0.1 -p 6379
```

* 关闭 Redis 客户端

```
redis-cli shutdown
```



### 二、Redis数据结构

Redis支持多种类型的数据结构，如 [字符串（strings）](http://redis.cn/topics/data-types-intro.html#strings)， [散列（hashes）](http://redis.cn/topics/data-types-intro.html#hashes)， [列表（lists）](http://redis.cn/topics/data-types-intro.html#lists)， [集合（sets）](http://redis.cn/topics/data-types-intro.html#sets)， [有序集合（sorted sets）](http://redis.cn/topics/data-types-intro.html#sorted-sets) 与范围查询， [bitmaps](http://redis.cn/topics/data-types-intro.html#bitmaps)， [hyperloglogs](http://redis.cn/topics/data-types-intro.html#hyperloglogs) 和 [地理空间（geospatial）](http://redis.cn/commands/geoadd.html) 索引半径查询。

Redis的数据结构如下图所示：





#### 三、Redis高可用

持久化

主从

哨兵

#### 四、Redis性能优化

reactor模型

多线程

#### 五、Redis应用场景

缓存

分布式锁



#### 附录

* [Redis下载与安装](https://www.cnblogs.com/liyihua/p/14482412.html?ivk\_sa=1024320u)
* [阿里云Redis开发规范](https://developer.aliyun.com/article/531067)
*
