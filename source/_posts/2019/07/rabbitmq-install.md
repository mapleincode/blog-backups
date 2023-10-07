---
title: RabbitMQ 安装 & 添加登录用户[转载]
date: 2019-01-11 00:00:46
tags: [ rabbitmq ]
---

> 本文转载自 [https://www.dev-heaven.com/posts/1914.html]()

##### 安装脚本

```
yum install wget -y

#Download RabbitMQ and Erlang
wget https://www.rabbitmq.com/releases/rabbitmq-server/v3.6.9/rabbitmq-server-3.6.9-1.el6.noarch.rpm
wget https://www.rabbitmq.com/releases/erlang/erlang-19.0.4-1.el6.x86_64.rpm

#Install Erlang and RabbitMQ
rpm -ivh erlang-19.0.4-1.el6.x86_64.rpm 
rpm --import http://www.rabbitmq.com/rabbitmq-signing-key-public.asc
yum install rabbitmq-server-3.6.9-1.el6.noarch.rpm -y
```

##### 发现错误

```
1. 安装依赖Requires: socat
2. 安装socat的时候提示需要tcp_wrappers
```

##### 解决方案

```
wget –no-cache http://www.convirture.com/repos/definitions/rhel/6.x/convirt.repo -O /etc/yum.repos.d/convirt.repo
yum install socat -y
使用https://rpmfind.net/linux/rpm2html/search.php搜索tcp_wrappers，下载，rpm -ivh tcp_wrappers.version
```

##### 启动RabbitMQ

```
chkconfig rabbitmq-server on
/sbin/service rabbitmq-server start
# 修改密码
rabbitmqctl change_password guest welcome123
# 启动管理界面
rabbitmq-plugins enable rabbitmq_management
```

##### 使用过程错误小记

###### user ‘guest’ - User can only log in via localhost

登录响应如下：

```
{"error":"not_authorised","reason":"User can only log in via localhost"}
```

我实在docker中创建rabbitmq的，然后将端口15672映射到宿主机器上的15672，所以也算是远程登录，而guest用户要求只能使用localhost登录，所以要想远程登录必须新建用户。

```
rabbitmqctl add_user {username} {password}
# 例：创建admin用户
rabbitmqctl add_user admin 123456
```

###### 新建用户无法登录

```
{"error":"not_authorised","reason":"Not management user"}
```

需要授予用户角色

```
rabbitmqctl set_user_tags {username} {role}
# 例
rabbitmqctl set_user_tags admin administrator
```

###### 用户授权

```
rabbitmqctl set_permissions [-p vhostpath] {user} {conf} {write} {read}
rabbitmqctl set_permissions -p / admin ".*" ".*" ".*"
```

##### 关于RabbitMQ权限和角色管理

参考：<http://blog.csdn.net/zyz511919766/article/details/42292655>

#### trace

调试消息的时候发现有些消息发出去了直接被丢弃了，但是也不知道怎么解决，这个时候其实可以打开log

```
rabbitmq-plugins list
rabbitmqctl enable rabbitmq_tracing
```

