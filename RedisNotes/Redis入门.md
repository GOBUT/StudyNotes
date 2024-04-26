# Redis入门

## 1、Redis简介

Redis（remove dictionary server）基于**内存**的NoSQL数据存储系统。

用途：数据库缓存、消息队列

支持以下类型：

![image-20240425134513024](RedisNotes/assets/image-20240425134513024-4023972.png)

常用方法：

![image-20240425134601138](RedisNotes/assets/image-20240425134601138-4023965.png)

Redis特点：

![image-20240425134644169](RedisNotes/assets/image-20240425134644169.png)

## 2、安装配置

### 2.1 安装

Mac：

```
brew install redis
```

Linux:

```
yum install redis
```

Windows:

1、可以使用docker安装

2、通过安装包安装

### 2.2  启动

Mac、Linux：

```
redis-server
```

Windows:

```
redis-server.exe
```

![image-20240425135131986](RedisNotes/assets/image-20240425135131986-4024294.png)

### 2.3 关闭Redis

Ctrl+C 或者是关闭终端

### 2.4 启动客户端

重开一个终端，然后输入

```
redis-cli
```

### 2.5 GUI工具RedisInsight的使用

下载地址：[官网](https://redis.io/insight/#insight-form)

### 2.6 GUI工具Another Redis Desktop Manager

下载地址：[官网](https://goanother.com/cn/#download)

## 3、语法

### 3.1 字符串

```python
# 设置键
SET name pu
# 获取值
GET name
# 删除键
DEL name
# 查看是否存在键
EXISTS name
# 查看有哪些键
KEYS *
# 表示查看以me结尾的键
KEYS *me 
# 删除所有键
FLUSHALL
# 清屏
clear
# 查看键的过期时间
TTL name
返回结果-1表示没有设置过期时间 
返回结果-2表示过期 
# 设置过期时间
EXPIRE name 10
设置为10秒
SETEX name 5 一键三连
表示name的键过期时间是5秒
```

支持中文的话需要用以下登录：

```
# 先退出
quit
# 再重登
redis-cli --raw
```

### 3.2 列表

```python
# 给列表增加元素（从头增加）
LPUSH ls a
可以一次性增加多个元素
LPUSH ls a b c d e
# 给列表增加元素（从尾增加）
RPUSH ls f
# 查看列表
LRANGE ls 0 -1
表示全部
显示的时候是 e d c b a，有点像栈
# 删除头部1个元素
LPOP ls
# 删除尾部1个元素
RPOP ls
# 查看列表长度
LLEN ls
# 删除指定范围外的元素
LTRIM ls 1 3
```

### 3.3 集合set

集合和列表的区别：列表中元素可以重复，集合中元素不可重复

```
# 添加元素
SADD st a
# 查询集合
SMEMBERS st
# 判断一个元素是否在集合中
SISMEMBER st a
1表示在，0表示不在
# 删除元素
SREM st a
```

### 3.4有序集合

有序集合中键不可重复，但是值可以重复

```
# 增加列
ZADD result 680 清华 660 北大 650 复旦
# 查看成员  
ZRANGE result 0 -1
# 查看成员和分数
ZRANGE result 0 -1 WITHSCORES
# 查看具体成员的分数
ZSCORE result 清华
# 查看排名
ZRANK result 清华
# 从大到小输出排名
ZREVRANK result 清华
```

其他命令：删除成员，删除/修改某个范围的成员，增加成员等等

### 3.5 哈希HASH

键值对的形式

```
# 增加
HSET person name zhangsan
HSET person age 100
# 获取
HGET person name
# 获取全部
HGETALL person
# 删除
HDEL person age
# 判断某个键值对是否存在
HEXISTS person name
# 获取哈希中所有的键
HKEYS person
# 长度
HLEN person
```

### 3.6 发布订阅模式

```
# 发布模式
publish gobut hello
# 订阅模式
subscribe gobut
```

缺点：无法持久化，无法记录历史消息

### 3.7 消息队列

```python
# 增加
> XADD gobut * course git
> XADD gobut * course python
# 查看长度
> XLEN gobut
# 显示所有
> xrange gobut - +
# 删除消息
> XDEL gobut 1714051124291-0
# 删除所有消息
> xtrim gobut maxlen 0
# 也可以自己定义序列号（ID，但是需要保证递增）
> FLUSHALL
OK
> XADD gobut 1-0 course Java
1-0
> XADD gobut 2-0 course php
2-0
> XADD gobut 3-0 course python
3-0
# 读取消息  BLOCK是阻塞1000毫秒  最后的0是索引   2是一次读2条
> XREAD COUNT 2 BLOCK 1000 STREAMS gobut 0
gobut
1-0
course
Java
2-0
course
php
```

创建消费者组：

```python
# 创建一个group1组
> xgroup create gobut group1 0
OK
# 查看组的信息
> xinfo groups gobut
name
group1
consumers
0
pending
0
last-delivered-id
0-0
entries-read
null
lag
4
# 给这个组添加三个消费者
> XGROUP CREATECONSUMER gobut group1 consumer1
1
> XGROUP CREATECONSUMER gobut group1 consumer2
1
> XGROUP CREATECONSUMER gobut group1 consumer3
1
# 读取消息 COUNT 2读取2条消息，BLOCK 3000阻塞3秒，>表示读取最新的消息
XREADGROUP GROUP group1 consumer1  COUNT 2 BLOCK 3000 STREAMS gobut >
```

### 3.8 地理空间

```python
# 增加   经纬度加上城市名
> GEOADD city 116.40 39.90 Beijing
1
# 增加多个
> GEOADD city 121.47 31.23 Shanghai 114.08 22.54 Shenzhen
2
# 查看指定城市的经纬度
> GEOPOS city Beijing
116.39999896287918091
39.90000009167092543
# 计算两个城市的直线距离（m）
> GEODIST city Beijing Shanghai
1067378.7564
# 计算两个城市的直线距离（km）
> GEODIST city Beijing Shanghai KM
1067.3788
# 以上海为中心围城一个圆，1200km内的城市
> GEOSEARCH city FROMMEMBER Shanghai BYRADIUS 1200 KM
Shanghai
Beijing
# 以上海为中心围城一个矩形，2000km内的城市
GEOSEARCH city FROMMEMBER Shanghai BYBOX 2000 2000 KM
```

### 3.9 HyperLogLog

主要功能是不重复统计（用于统计UV之类的）

```python
# 增加
> PFADD course git docker redis
1
# 个数
> PFCOUNT course
3
> PFADD course nginx
1
> PFCOUNT course
4
> PFADD course2 python git nginx
1
# 合并两个键
> PFMERGE result course course2
OK
> PFCOUNT result
5

```

### 3.10 位图

本质是字符串的拓展，只有01两种状态，可以用来记录登录情况，点赞情况，收藏情况等等。

```python
# 设置单个位图
> SETBIT dianzan 0 1
0
> SETBIT dianzan 1 0
0
> GETBIT dianzan 0
1
# 因为位图的本质是字符串，所以可以使用SET快速设置 xf0就是二进制的11110000
> SET dianzan "\xF0"
OK
> GETBIT dianzan 0
1
# 获取位图中1的个数
> BITCOUNT dianzan
4
# 这里是获取0开始的位置为4
> BITPOS dianzan 0
4
> BITPOS dianzan 1
0
```

### 3.11 位域

位域可以将很多较小的整数存储到一个较大的位图中，从而更加高效的使用内存。

![image-20240426113248973](RedisNotes/assets/image-20240426113248973-4102370.png)

```python
# 设置玩家1第一个域等价为1
> BITFIELD player:1 set u8 #0 1
0
> GET player:1

# 查看玩家1等级
> BITFIELD player:1 get u8 #0
1
# 设置玩家1初始金币为100
> BITFIELD player:1 set u32 #1 100
0
> BITFIELD player:1 get u32 #1
100
# 给玩家1增加100个金币
> BITFIELD player:1 incrby u32 #1 100
200
```

## 4、事务

![image-20240426133404869](RedisNotes/assets/image-20240426133404869-4109646.png)

MULTI用于开启一个事务，最后通过EXEC来执行事务中的所有命令

在EXEC命令之前，所有的事务都会放入一个队列中缓存起来，不会立即执行，事务中有命令执行失败，其他命令依然会被执行

事务不能保证所有都成功，取决于事务的命令

![image-20240426133555911](RedisNotes/assets/image-20240426133555911-4109757.png)

```
> MULTI
OK
> SET k1 v1
QUEUED
> set k2 v2
QUEUED
> EXEC
OK
OK
```

## 5、持久化

因为Redis是基于内存的数据库，所以持久化很重要

持久化有两种模式：RDB(Redis Database)、AOF（Append Only File）

> RDB:使用save来存储快照，可以设置3600秒内修改一次就生成快照，也可以设置300秒内修改10000次才生成快照，可以自己设置，也可以手动报错。
>
> 缺点：如果发生宕机，最后一次保存快照后的内容不会被保存。
>
> 内存同步到硬盘的这个过程持续比较长，这段时间一直处于阻塞状态，不能接受任何请求。解决这个问题：使用bgsave，单独创建一个子进程，将内存中的数据写入到硬盘中。这样的话主进程就可以继续处理其他请求了。（会有一定的性能损耗）



> AOF:在每一次执行命令的时候同时也写入到文件中（有点像日志），当发生宕机重启的时候，会从AOF文件中读取命令并执行一遍。



## 6、主从复制

一个主节点可以有多个从节点，数据的复制是单向的，只能由主节点到从节点。

一般来说：主节点负责写操作，从节点负责读操作，主节点会将自己的操作通过异步的方式发送给从节点，从节点接收到以后更新自己的数据，这样就达到了数据一致的目的。

## 7、哨兵模式

为了实现自动的故障转移（主节点宕机后从节点提升为主节点），哨兵以一个独立的进程运行在Redis中，用来监控Redis中各个节点是否运行正常。

功能：

监控、通知、自动故障转移

```python
# 1、使用vim新建一个sentinel.conf文件
vim sentinel.conf
# 文件中的内容，1表示只需要1个哨兵节点同意就可以进行故障转移
entinel monitor master 127.0.0.1 6379 1
# 2、启动哨兵模式
redis-sentinel sentinel.conf
```

高可用一般是三个节点选举主节点

