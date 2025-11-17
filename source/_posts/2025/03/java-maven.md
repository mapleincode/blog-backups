---
title: setting.xml 配置 (by deepseek)
date: 2025-03-06 15:17:49
tags: [java, mvn]
---



### **完整的 `settings.xml` 配置**

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 https://maven.apache.org/xsd/settings-1.0.0.xsd">

    <!-- 本地仓库路径配置 -->
    <localRepository>/path/to/local/repo</localRepository>

    <!-- 是否以离线模式运行 -->
    <offline>false</offline>

    <!-- 插件组配置 -->
    <pluginGroups>
        <!-- 插件组 ID，用于简化插件前缀 -->
        <pluginGroup>com.example.plugins</pluginGroup>
    </pluginGroups>

    <!-- 代理服务器配置 -->
    <proxies>
        <!-- 代理配置 -->
        <proxy>
            <id>example-proxy</id>
            <active>true</active> <!-- 是否启用 -->
            <protocol>http</protocol> <!-- 代理协议 -->
            <host>proxy.example.com</host> <!-- 代理主机 -->
            <port>8080</port> <!-- 代理端口 -->
            <username>proxy-user</username> <!-- 代理用户名 -->
            <password>proxy-password</password> <!-- 代理密码 -->
            <nonProxyHosts>*.example.com|localhost</nonProxyHosts> <!-- 不使用代理的主机 -->
        </proxy>
    </proxies>

    <!-- 服务器认证配置 -->
    <servers>
        <!-- 仓库认证信息 -->
        <server>
            <id>central</id> <!-- 仓库 ID，与 pom.xml 或镜像配置中的 ID 对应 -->
            <username>repo-user</username> <!-- 用户名 -->
            <password>repo-password</password> <!-- 密码 -->
        </server>
        <server>
            <id>thirdparty-repo</id>
            <username>thirdparty-user</username>
            <password>thirdparty-password</password>
        </server>
    </servers>

    <!-- 镜像仓库配置 -->
    <mirrors>
        <!-- 中央仓库镜像 -->
        <mirror>
            <id>aliyun-mirror</id> <!-- 镜像 ID -->
            <url>https://maven.aliyun.com/repository/public</url> <!-- 镜像地址 -->
            <mirrorOf>central</mirrorOf> <!-- 镜像的目标仓库 -->
        </mirror>

        <!-- 第三方仓库镜像 -->
        <mirror>
            <id>internal-mirror</id>
            <url>https://internal-repo.example.com/repo</url>
            <mirrorOf>thirdparty</mirrorOf>
        </mirror>

        <!-- 匹配所有仓库的镜像 -->
        <mirror>
            <id>fallback-mirror</id>
            <url>https://fallback.example.com/repo</url>
            <mirrorOf>*</mirrorOf> <!-- 匹配所有仓库 -->
        </mirror>
    </mirrors>

    <!-- 配置文件激活配置 -->
    <profiles>
        <!-- 默认激活的 Profile -->
        <profile>
            <id>default-profile</id>
            <activation>
                <activeByDefault>true</activeByDefault> <!-- 默认激活 -->
            </activation>
            <repositories>
                <!-- 中央仓库配置 -->
                <repository>
                    <id>central</id>
                    <url>https://repo.maven.apache.org/maven2</url>
                    <releases>
                        <enabled>true</enabled> <!-- 启用正式版本 -->
                    </releases>
                    <snapshots>
                        <enabled>false</enabled> <!-- 禁用快照版本 -->
                    </snapshots>
                </repository>

                <!-- 第三方仓库配置 -->
                <repository>
                    <id>thirdparty-repo</id>
                    <url>https://example.com/repo</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                </repository>
            </repositories>
            <pluginRepositories>
                <!-- 插件仓库配置 -->
                <pluginRepository>
                    <id>central</id>
                    <url>https://repo.maven.apache.org/maven2</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>false</enabled>
                    </snapshots>
                </pluginRepository>
            </pluginRepositories>
        </profile>

        <!-- 根据环境变量激活的 Profile -->
        <profile>
            <id>env-profile</id>
            <activation>
                <property>
                    <name>env</name> <!-- 环境变量名称 -->
                    <value>prod</value> <!-- 环境变量值 -->
                </property>
            </activation>
            <properties>
                <!-- 自定义属性 -->
                <build.dir>target/prod</build.dir>
            </properties>
        </profile>
    </profiles>

    <!-- 激活的 Profile -->
    <activeProfiles>
        <activeProfile>default-profile</activeProfile> <!-- 默认激活的 Profile -->
    </activeProfiles>
</settings>
```



运行 HTML

------

### **配置项说明**

1. **`<localRepository>`**：
   - 指定本地仓库的路径。默认路径是 `~/.m2/repository`。
2. **`<offline>`**：
   - 是否以离线模式运行 Maven。默认为 `false`。
3. **`<pluginGroups>`**：
   - 配置插件组，用于简化插件前缀。
4. **`<proxies>`**：
   - 配置代理服务器，用于访问外部仓库。
5. **`<servers>`**：
   - 配置仓库的认证信息（用户名和密码）。
6. **`<mirrors>`**：
   - 配置镜像仓库，用于替换或加速特定仓库的访问。
7. **`<profiles>`**：
   - 定义 Maven 的构建配置文件（Profile），可以根据条件激活不同的配置。
   - 每个 Profile 可以包含 `<repositories>`、`<pluginRepositories>` 和 `<properties>` 等配置。
8. **`<activeProfiles>`**：
   - 指定默认激活的 Profile。

------

### **常见使用场景**

1. **加速依赖下载**：

   - 配置阿里云镜像仓库：

     xml

     复制

     ```
     <mirror>
         <id>aliyun-mirror</id>
         <url>https://maven.aliyun.com/repository/public</url>
         <mirrorOf>central</mirrorOf>
     </mirror>
     ```

     

     运行 HTML

2. **私有仓库认证**：

   - 配置私有仓库的用户名和密码：

     xml

     复制

     ```
     <server>
         <id>private-repo</id>
         <username>user</username>
         <password>password</password>
     </server>
     ```

     

     运行 HTML

3. **多环境配置**：

   - 根据环境变量激活不同的 Profile：

     xml

     复制

     ```
     <profile>
         <id>prod-profile</id>
         <activation>
             <property>
                 <name>env</name>
                 <value>prod</value>
             </property>
         </activation>
         <properties>
             <build.dir>target/prod</build.dir>
         </properties>
     </profile>
     ```

     

     运行 HTML

------

### **总结**

- `settings.xml` 是 Maven 的全局配置文件，用于配置仓库、镜像、代理、认证等信息。
- 通过合理配置 `settings.xml`，可以优化 Maven 的构建效率，支持多环境构建，并管理私有仓库的访问权限。
- 以上配置模板可以根据实际需求进行调整和扩展。