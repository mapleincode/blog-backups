---
title: mvn 笔记精简
date: '2022-04-07 11:10:43'
tags: [java, mvn]
---

# Maven 笔记精简

from https://segmentfault.com/a/1190000019897882

## Maven 定位

- 编译
- 部署
- 清理
- 测试
- 报告
- 打包
- 安装



## 目录架构约定

```shell
test
  / main
  		/ java     // 源代码
  		resources  // 配置文件
  / test
  	  / java     // 测试代码
```





## 配置文件

`pom.xml` 



## 配置结构

```xml
<dependencies>
	<dependency>
  	<groupId>cn.vmkoo.test</groupId>
    <artifactId>test</artifactId>
    <version>1.0</version>
    <scope>compile</scope>
  </dependency>
</dependencies>
```

- groupId
  - 公司或组织的域名倒序 + 当前项目名称
- artifactId
  - 当前项目模块名称
- version
  - 模块版本号



## Maven 中央仓库

https://mvnrepository.com/



# 仓库

- 本地仓库
- 远程仓库
  - 私服
  - 中央仓库
  - 中央仓库镜像



## 生命周期 / 插件 / 目标





