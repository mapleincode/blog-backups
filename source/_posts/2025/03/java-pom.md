---
title: Java pom.xml 范例和说明 (by deepseek)
date: 2025-03-06 15:37:27
tags: [java, maven]
---



以下是一个完整的 `pom.xml` 配置范例，包含详细的注释说明。这个配置文件涵盖了常见的配置项，如项目基本信息、依赖管理、插件配置、仓库配置等。

------

### **完整的 `pom.xml` 配置范例**

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <!-- 项目基本信息 -->
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId> <!-- 组织或公司标识 -->
    <artifactId>my-project</artifactId> <!-- 项目名称 -->
    <version>1.0.0</version> <!-- 项目版本 -->
    <packaging>jar</packaging> <!-- 打包方式，默认为 jar -->

    <!-- 项目名称和描述 -->
    <name>My Project</name>
    <description>A sample Maven project</description>

    <!-- 项目属性 -->
    <properties>
        <java.version>11</java.version> <!-- Java 版本 -->
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding> <!-- 源码编码 -->
        <maven.compiler.source>${java.version}</maven.compiler.source> <!-- 源码兼容版本 -->
        <maven.compiler.target>${java.version}</maven.compiler.target> <!-- 目标字节码版本 -->
    </properties>

    <!-- 依赖管理 -->
    <dependencies>
        <!-- JUnit 测试依赖 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope> <!-- 作用范围为测试 -->
        </dependency>

        <!-- Spring Boot Web 依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>2.5.4</version>
        </dependency>

        <!-- 其他依赖 -->
        <dependency>
            <groupId>com.google.guava</groupId>
            <artifactId>guava</artifactId>
            <version>30.1.1-jre</version>
        </dependency>
    </dependencies>

    <!-- 构建配置 -->
    <build>
        <!-- 插件配置 -->
        <plugins>
            <!-- Maven 编译器插件 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                </configuration>
            </plugin>

            <!-- Spring Boot Maven 插件 -->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>2.5.4</version>
                <configuration>
                    <mainClass>com.example.MyApplication</mainClass> <!-- 主类 -->
                </configuration>
            </plugin>

            <!-- Maven 打包插件 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>3.2.0</version>
                <configuration>
                    <archive>
                        <manifest>
                            <addClasspath>true</addClasspath>
                            <mainClass>com.example.MyApplication</mainClass> <!-- 主类 -->
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
        </plugins>

        <!-- 资源文件配置 -->
        <resources>
            <resource>
                <directory>src/main/resources</directory> <!-- 资源文件目录 -->
                <filtering>true</filtering> <!-- 是否启用资源过滤 -->
            </resource>
        </resources>

        <!-- 测试资源文件配置 -->
        <testResources>
            <testResource>
                <directory>src/test/resources</directory> <!-- 测试资源文件目录 -->
                <filtering>true</filtering>
            </testResource>
        </testResources>
    </build>

    <!-- 仓库配置 -->
    <repositories>
        <!-- 中央仓库 -->
        <repository>
            <id>central</id>
            <url>https://repo.maven.apache.org/maven2</url>
        </repository>

        <!-- 第三方仓库 -->
        <repository>
            <id>thirdparty-repo</id>
            <url>https://example.com/repo</url>
        </repository>
    </repositories>

    <!-- 插件仓库配置 -->
    <pluginRepositories>
        <!-- 中央插件仓库 -->
        <pluginRepository>
            <id>central</id>
            <url>https://repo.maven.apache.org/maven2</url>
        </pluginRepository>
    </pluginRepositories>

    <!-- 依赖管理 -->
    <dependencyManagement>
        <dependencies>
            <!-- Spring Boot 依赖管理 -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>2.5.4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <!-- 项目分发管理 -->
    <distributionManagement>
        <!-- 正式版本仓库 -->
        <repository>
            <id>release-repo</id>
            <url>https://example.com/releases</url>
        </repository>

        <!-- 快照版本仓库 -->
        <snapshotRepository>
            <id>snapshot-repo</id>
            <url>https://example.com/snapshots</url>
        </snapshotRepository>
    </distributionManagement>
</project>
```



运行 HTML

------

### **配置项说明**

1. **项目基本信息**：
   - `<groupId>`：组织或公司的唯一标识。
   - `<artifactId>`：项目的唯一标识。
   - `<version>`：项目版本。
   - `<packaging>`：打包方式（如 jar、war 等）。
2. **项目属性**：
   - `<properties>`：定义项目属性，如 Java 版本、编码格式等。
3. **依赖管理**：
   - `<dependencies>`：定义项目依赖。
   - `<dependencyManagement>`：统一管理依赖版本。
4. **构建配置**：
   - `<build>`：配置构建过程，包括插件和资源文件。
   - `<plugins>`：配置 Maven 插件（如编译器插件、打包插件等）。
   - `<resources>`：配置资源文件目录。
5. **仓库配置**：
   - `<repositories>`：配置项目依赖的远程仓库。
   - `<pluginRepositories>`：配置插件依赖的远程仓库。
6. **分发管理**：
   - `<distributionManagement>`：配置项目构建产物的发布仓库。

------

### **常见使用场景**

1. **多模块项目**：
   - 使用 `<modules>` 配置子模块。
   - 使用 `<parent>` 配置父模块。
2. **自定义打包**：
   - 使用 `maven-assembly-plugin` 或 `maven-shade-plugin` 定制打包方式。
3. **环境配置**：
   - 使用 `<profiles>` 配置不同环境的构建参数。

------

### **总结**

- `pom.xml` 是 Maven 项目的核心配置文件，用于管理项目的依赖、构建和发布。
- 通过合理配置 `pom.xml`，可以优化项目的构建流程，管理依赖版本，并支持多环境构建。
- 以上配置模板可以根据实际需求进行调整和扩展。