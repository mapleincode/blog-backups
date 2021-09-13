---
title: systemctl 命令(转)
date: 2018-04-23 15:33:58
tags: [ linux ]
---

**systemctl命令**是系统服务管理器指令，它实际上将 [service](http://man.linuxde.net/service) 和 [chkconfig](http://man.linuxde.net/chkconfig) 这两个命令组合到一起。

| 任务                 |                            旧指令                            | 新指令                                                       |
| -------------------- | :----------------------------------------------------------: | ------------------------------------------------------------ |
| 使某服务自动启动     | chkconfig --level 3 [httpd](http://man.linuxde.net/httpd) on | systemctl [enable](http://man.linuxde.net/enable) httpd.service |
| 使某服务不自动启动   |                chkconfig --level 3 httpd off                 | systemctl disable httpd.service                              |
| 检查服务状态         |                     service httpd status                     | systemctl status httpd.service （服务详细信息） systemctl is-active httpd.service （仅显示是否 Active) |
| 显示所有已启动的服务 |                       chkconfig --list                       | systemctl list-units --[type](http://man.linuxde.net/type)=service |
| 启动某服务           |                     service httpd start                      | systemctl start httpd.service                                |
| 停止某服务           |                      service httpd stop                      | systemctl stop httpd.service                                 |
| 重启某服务           |                    service httpd restart                     | systemctl restart httpd.service                              |

### 实例

1.启动 nfs 服务

```
systemctl start nfs-server.service
```

2.设置开机自启动

```
systemctl enable nfs-server.service
```

3.停止开机自启动

```
systemctl disable nfs-server.service
```

4.查看服务当前状态

```
systemctl status nfs-server.service
```

5.重新启动某服务

```
systemctl restart nfs-server.service
```

6.查看所有已启动的服务

```
systemctl list -units --type=service
```
