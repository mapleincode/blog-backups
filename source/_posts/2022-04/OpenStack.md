---
title: OpenStack 部署
date: 2022-05-11 13:10:21
tags: openstack
---



## 密码和描述的关系

密码名称

描述

数据库密码(不能使用变量)

数据库的root密码

```
ADMIN_PASS
```

`admin` 用户密码

```
CEILOMETER_DBPASS
```

Telemetry 服务的数据库密码

```
CEILOMETER_PASS
```

Telemetry 服务的 `ceilometer` 用户密码

```
CINDER_DBPASS
```

块设备存储服务的数据库密码

```
CINDER_PASS
```

块设备存储服务的 `cinder` 密码

```
DASH_DBPASS`Database password for the dashboard`DEMO_PASS
```

`demo` 用户的密码

```
GLANCE_DBPASS
```

镜像服务的数据库密码

```
GLANCE_PASS
```

镜像服务的 `glance` 用户密码

```
HEAT_DBPASS
```

Orchestration服务的数据库密码

```
HEAT_DOMAIN_PASS
```

Orchestration 域的密码

```
HEAT_PASS
```

Orchestration 服务中``heat``用户的密码

```
KEYSTONE_DBPASS
```

认证服务的数据库密码

```
NEUTRON_DBPASS
```

网络服务的数据库密码

```
NEUTRON_PASS
```

网络服务的 `neutron` 用户密码

```
NOVA_DBPASS
```

计算服务的数据库密码

```
NOVA_PASS
```

计算服务中``nova``用户的密码

```
RABBIT_PASS
```

RabbitMQ的guest用户密码

```
SWIFT_PASS
```

对象存储服务用户``swift``的密码