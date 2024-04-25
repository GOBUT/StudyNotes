# Redis入门

## 1、Redis简介

Redis（remove dictionary server）基于**内存**的NoSQL数据存储系统。

用途：数据库缓存、消息队列

支持以下类型：

![image-20240425134513024](/assets/image-20240425134513024-4023972.png)

常用方法：

![image-20240425134601138](/assets/image-20240425134601138-4023965.png)

Redis特点：

![image-20240425134644169](/assets/image-20240425134644169.png)

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

![image-20240425135131986](/assets/image-20240425135131986-4024294.png)

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

