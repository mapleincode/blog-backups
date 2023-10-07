---
date: 2022-04-19 15:32:22
tags: [java, mvn]
title: "30 个 Maven 命令"
---

<https://www.cnblogs.com/javastack/p/12982472.html>



maven 命令除了常用的几个，大部分经常记不住，整理一下，方便查询。

**maven 命令的格式为 mvn [plugin-name]:[goal-name]，可以接受的参数如下。**

```shell
-D 指定参数，如 -Dmaven.test.skip=true 跳过单元测试；

-P 指定 Profile 配置，可以用于区分环境；

-e 显示maven运行出错的信息；

-o 离线执行命令,即不去远程仓库更新包；

-X 显示maven允许的debug信息；

-U 强制去远程更新snapshot的插件或依赖，默认每天只更新一次。
```



## 常用maven命令

1. **创建maven项目：mvn archetype:create**

   - 指定 group： -DgroupId=packageName

   - 指定 artifact：-DartifactId=projectName

   - 创建web项目：-DarchetypeArtifactId=maven-archetype-webapp

2. **创建maven项目：mvn archetype:generate**

3. 验证项目是否正确：mvn validate

4. **maven 打包：mvn package**

5. 只打 jar 包：mvn jar:jar

6. 生成源码 jar 包：mvn source:jar

7. 产生应用需要的任何额外的源代码：mvn generate-sources

8. 编译源代码： mvn compile

9. 编译测试代码：mvn test-compile

10. 运行测试：mvn test

11. 运行检查：mvn verify

12. 清理maven项目：mvn clean

13. 生成eclipse项目：mvn eclipse:eclipse

14. 清理eclipse配置：mvn eclipse:clean

15. 生成idea项目：mvn idea:idea

16. **安装项目到本地仓库：mvn install**

17. 发布项目到远程仓库：mvn:deploy

18. 在集成测试可以运行的环境中处理和发布包：mvn integration-test

19. 显示maven依赖树：mvn dependency:tree

20. 显示maven依赖列表：mvn dependency:list

21. 下载依赖包的源码：mvn dependency:sources

22. 安装本地jar到本地仓库：mvn install:install-file -DgroupId=packageName -DartifactId=projectName -Dversion=version -Dpackaging=jar -Dfile=path



## web项目相关命令

1. **启动tomcat：mvn tomcat:run**
2. 启动jetty：mvn jetty:run
3. 运行打包部署：mvn tomcat:deploy
4. 撤销部署：mvn tomcat:undeploy
5. 启动web应用：mvn tomcat:start
6. 停止web应用：mvn tomcat:stop
7. 重新部署：mvn tomcat:redeploy
8. 部署展开的war文件：mvn war:exploded tomcat:exploded