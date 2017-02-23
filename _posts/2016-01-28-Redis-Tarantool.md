---
layout: post
title: 基于Java Client的Redis与Tarantool HASH性能对比
categories: [java, redis, Tarantool]
description: 基于Java Client的Redis与Tarantool HASH性能对比
keywords: Redis, Tarantool
---

##前言
- Tarantool 是一款高性能Key/Value in-memory NoSQL database 最新版本为1.6.7 http://tarantool.org/
- Redis 现在使用非常多的内存 Key/Value数据库，多用于缓存业务场景。最新版本为 3.0.6 http://www.redis.io/
- tarantool-java最新版本为1.6 https://github.com/tarantool/tarantool-java/ 是Tarantool的java connector
- Jedis最新版本为2.8 https://github.com/xetorthio/jedis 是Redis java client
- 关于Tarantool的安装可参考前篇文章，Redis的安装google可查到一堆资料。总之先把官网文档通读是非常好的习惯。
- 基于当前版本java client的测试

##目的
本文基于文件去重的一个简单业务场景进行的测试，不能完全判定那款的性能高于那款，只是给读者一个可视性的参考。为什么写此文是因为之前看到一篇译文http://www.open-open.com/news/view/dbaaf4 原文：http://highscalability.com/blog/2015/12/30/how-to-choose-an-in-memory-nosql-solution-performance-measur.html  作者使用YCSB测试了主流的NoSQL数据库，最后得出的结论是【Tarantool在所有负载类型测试中皆取得了最优成绩。它创建了一个无锁内存引擎，以协同多任务方式进行操作而不是互斥或并行处理方式。根据以下性能图表现，我们的结论是Tarantool的高吞吐量处理是其最大优势之一。因此在多数场合下，Tarantool是用户的最佳选择。】于是对Tarantool产生了极大的兴趣。


##准备
1.Tarantool 准备
```
box.schema.space.create('storage')
box.space.storage:create_index('primary', {type = 'hash', parts = {1, 'STR'}})
box.schema.user.create('storage', { password = 'storage' })
box.schema.user.grant('storage', 'execute,read,write', 'universe')
box.space['storage']:format{{name='md5', type='str'},{name='filePath', type='str'}}
```
- 安装请参看上篇文章
- 创建空间storage
- 创建主索引
- 创建访问用户storage
- storage用户权限设置
- 空间kv类型格式化，md5为key 主键索引 ，filePath value
这就是我们基本业务场景的数据结构，key为MD5值，value为对应的文件地址

2.Redis 准备

 -  安装运行

 ```
wget http://download.redis.io/releases/redis-3.0.6.tar.gz
tar xzf redis-3.0.6.tar.gz
make  && make install
cd redis-3.0.6
src/redis-server
```

3.操作系统准备

 - CentOS6.5 2G 1core


##开始

  双方测试代码均使用最基本直连方式测试，且Redis 使用HASH 方式。

  ```
  TarantoolConnection16 con = new TarantoolConnection16Impl("192.168.254.133", 3301);
 ...
Jedis jedis = new Jedis("192.168.254.133", 6379);
  ```
(1). tarantool 分别操作10000,100000,1000000条记录的完成时间
   ![这里写图片描述](http://img.blog.csdn.net/20160128171951321)

(2). Redis分别操作10000,100000,1000000条记录的完成时间
   ![这里写图片描述](http://img.blog.csdn.net/20160128174928708)
注：在Redis使用直连hash set一百万条数据的时候java始终报异常，所以这里的值是0，在使用pipline后可以看到效率有非常大的提高，所以灵活的运用Redis的批量操作工具会带来意想不到性能提升！

(3).在使用多连接数进行操作的时候Tarantool的平均时间也是超过Redis的

##结语

在这个业务场景中基于Java Client的测试Redis是优于Tarantool的，不知道国内是否有公司使用或者研究过Tarantool？在我看来Tarantool还是非常优秀的，详细可以参看官方文档。他的中文资料还是相对比较少。不过对于搞技术的人来说，没有中文资料并不能阻碍学习。希望有使用经验的相互同学多多交流，有不对的地方也多多指正，共同学习进步。
