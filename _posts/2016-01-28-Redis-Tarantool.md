---
layout: post
title: 基于Java Client的Redis与Tarantool HASH性能对比
categories: [java, redis, Tarantool]
description: 基于Java Client的Redis与Tarantool HASH性能对比
keywords: Redis, Tarantool
---




## 前言
- Tarantool 是一款高性能Key/Value in-memory NoSQL database 最新版本为1.6.7 http://tarantool.org/
- Redis 现在使用非常多的内存 Key/Value数据库，多用于缓存业务场景。最新版本为 3.0.6 http://www.redis.io/
- tarantool-java最新版本为1.6 https://github.com/tarantool/tarantool-java/ 是Tarantool的java connector
- Jedis最新版本为2.8 https://github.com/xetorthio/jedis 是Redis java client
- 关于Tarantool的安装可参考前篇文章，Redis的安装google可查到一堆资料。总之先把官网文档通读是非常好的习惯。
- 基于当前版本java client的测试

## 目的
本文基于文件去重的一个简单业务场景进行的测试，不能完全判定那款的性能高于那款，只是给读者一个可视性的参考为什么写此文是因为之前看到一篇译文 http://www.open-open.com/news/view/dbaaf4  原文:http://highscalability.com/blog/2015/12/30/how-to-choose-an-in-memory-nosql-solution-performance-measur.html 作者使用YCSB测试了主流的NoSQL数据库，最后得出的结论是【Tarantool在所有负载类型测试中皆取得了最优成绩。它创建了一个无锁内存引擎，以协同多任务方式进行操作而不是互斥或并行处理方式。根据以下性能图表现，我们的结论是Tarantool的高吞吐量处理是其最大优势之一。因此在多数场合下，Tarantool是用户的最佳选择。】于是对Tarantool产生了极大的兴趣
