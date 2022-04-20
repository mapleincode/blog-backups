---
title: mvn 笔记精简
date: '2022-04-07 11:10:43'
tags: [java, mvn]
---

# Maven 概念

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

### 三套生命周期

- **Clean Lifecycle**

  - `mvn clean`
  - 生命周期
    - pre-clean
    - clean
    - post-clean

- **Default Lifecycle**

  - `mvn install`
  - 生命周期
    -  validate
    -   generate-sources
    -	 process-sources
    - generate-resources
    -  **process-resources**          
       - **复制并处理资源文件，至目标目录，准备打包。**
    -  **compile**
       - **编译项目的源代码。**
    - process-classes
    - generate-test-sources   
    -  process-test-sources
    -  generate-test-resources
    -  **process-test-resources**          
       -  **复制并处理资源文件，至目标测试目录。**
    -  **test-compile**
       - **编译测试源代码。**
    -  process-test-classes
    -  **test**                
    -  **使用合适的单元测试框架运行测试。这些测试代码不会被打包或部署。**
    -  prepare-package
    -  **package**          
    -  **接受编译好的代码，打包成可发布的格式，如  JAR  。**
    -  pre-integration-test
    -  integration-test
    -  post-integration-test
    -  verify
    -  install          
    -  将包安装至本地仓库，以让其它项目依赖。
    -  deploy          
    -  将最终的包复制到远程的仓库，以让其它开发人员与项目共享

  

- **Site Lifecycle**

  - `mvn site`
  - 生命周期
    - pre-site
    - site
    - post-site
    - site-deploy



## 依赖 & 依赖的范围

|                    | complie     | test  | provided        |
| ------------------ | ----------- | ----- | --------------- |
| 对主程序是否有效   | √           | x     | √               |
| 对测试程序是否有效 | √           | √     | √               |
| 是否参与打包       | √           | x     | x               |
| 是否参与部署       | √           | x     | x               |
| 例子               | spring-core | junit | servlet-api.tar |



#### 依赖原则

1. 路径最短者优先
2. 路径相同时，先声明者优先



### 继承

父工程

```xml
<groupId>com.cxkStudy.maven</groupId>
<artifactId>ParentName</artifactId>
<version>0.0.1-SNAPSHOT</version>
<description>学习Maven使用</description>
<packaging>pom</packaging> 
<modules>
    <module>../studyDenpen</module>
    <module>../Hello</module>
</modules>
```

子工程

```xml
<parent>
		<groupId>com.cxkStudy.maven</groupId>
		<artifactId>Parent</artifactId>
		<version>0.0.1-SNAPSHOT</version>
</parent>
```

1. 统一安装清理工作
2. 子工程的依赖不需要写版本号（会自动参照父依赖的版本）