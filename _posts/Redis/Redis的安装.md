---
title: Redis的安装
toc: false
permalink: /posts/redis/install.html
categories: Redis
date: 2019-01-01
---

介绍在 Redis 各种模式的安装，安装环境为 CentOS 7.6 64位。

## 单机模式

```shell
[redis@hiwzc ~]$ wget http://download.redis.io/releases/redis-5.0.7.tar.gz
[redis@hiwzc ~]$ tar xzf redis-5.0.7.tar.gz
[redis@hiwzc ~]$ cd redis-5.0.7
[redis@hiwzc ~]$ make
[redis@hiwzc ~]$ make PREFIX=/opt/redis install
[redis@hiwzc ~]$ cp redis.conf redis_master.conf
[redis@hiwzc ~]$ vim redis_master.conf
[redis@hiwzc ~]$ diff redis_master.conf redis.conf
# 这里开启了远程登陆并设置了密码
69c69
< #bind 127.0.0.1
---
> bind 127.0.0.1
507c507
< requirepass xxxxxxxxxxx
---
> # requirepass foobared
[redis@hiwzc ~]$ /opt/redis/bin/redis-server redis_master.conf
```

客户端登陆

```shell
/opt/redis/bin/redis-cli -h 47.91.xx.xx -p 6379 -a password
```

## 主从模式

```shell
[redis@hiwzc conf]$ diff redis_master.conf redis_slave.conf
92c92
< port 6379
---
> port 6380
286c286
< # replicaof <masterip> <masterport>
---
> replicaof 127.0.0.1 6379
293c293
< # masterauth <master-password>
---
> masterauth xxxx
507c507
< requirepass xxx
---
> requirepass xxxx

[redis@hiwzc conf]$ /opt/redis/bin/redis-server redis_master.conf &
[redis@hiwzc conf]$ /opt/redis/bin/redis-server redis_slave.conf &

```

客户端A

```shell
/opt/redis/bin/redis-cli -h 47.91.xx.xx -p 6379 -a password
set name hiwzc.com
```

客户端B

```shell
/opt/redis/bin/redis-cli -h 47.91.xx.xx -p 6380 -a password
get name
```

## 集群模式

TODO
