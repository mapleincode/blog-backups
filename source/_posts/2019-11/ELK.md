---
date: 2019-11-12 10:56:21
title: ELK 搭建
tags: ELK
---



# 起因

- 项目需求
- 公司某位大佬有记录文档可以参考





# ELK

ELK 平台是当前应用比较广泛的一套开源实时日志分析解决方案。ELK 由 ElasticSearch、Logstash 和 Kiabana 三个开源工具组成。

ELK = Elasticsearch + Logstash + Kibana

参考 & 下载地址: https://www.elastic.co/cn/products/

> MacOS 

## Elasticsearch

![](/Users/maple/Desktop/SynologyDrive/code-try/blog-backups/source/_posts/2019-11/assets/logo-elastic-search-color-64.svg)

Elasticsearch 是一个基于 JSON 的分布式搜索和分析引擎。



## Kibana

![](/Users/maple/Desktop/SynologyDrive/code-try/blog-backups/source/_posts/2019-11/assets/logo-kibana-64-color.svg)

Kibana 可谓 Elastic Stack 的窗户。探索数据并管理堆栈。



## Beats

![](/Users/maple/Desktop/SynologyDrive/code-try/blog-backups/source/_posts/2019-11/assets/logo-beats-64-color.svg)

Beats 是一个面向轻量型采集器的平台，这些采集器可从边缘机器发送数据。



## Logstash

![](/Users/maple/Desktop/SynologyDrive/code-try/blog-backups/source/_posts/2019-11/assets/logo-logstash-64-color.svg)

Logstash 是动态数据收集管道，拥有可扩展的插件生态系统。



# 搭建 ELK

> 官网的下载的速度真的，还是提前下载好安装包备用

| 产品          | 下载地址 (Linux 64 bits)                                     |
| ------------- | ------------------------------------------------------------ |
| Elasticsearch | https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.4.2-linux-x86_64.tar.gz |
| Kibana        | https://artifacts.elastic.co/downloads/kibana/kibana-7.4.2-linux-x86_64.tar.gz |
| Beats         | https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.4.2-linux-x86_64.tar.gz |
| Logstash      | https://artifacts.elastic.co/downloads/logstash/logstash-7.4.2.tar.gz |





### 1. 创建应用用户

教程有推荐创建一个新用户 elk-test 。但是创建用户就有一堆账户和权限问题需要操作，总的来看成本抬高了。

所以我这边打算安装在默认用户目录。

### 2. 安装 Java 运行环境

略

### 3. 安装 ELK

#### 3.1 下载 ElasticSearch & 安装 x-pack 

> x-pack 用于安全认证，可以不安装

```bash
$: su elk-test
$: mkdir elk
$: cd elk/
$: wget http://pi.file.mpae.cc/%E5%AE%89%E8%A3%85%E5%8C%85/elk/elasticsearch-7.4.2-linux-x86_64.tar.gz
$: tar -xvf elasticsearch-7.4.2-linux-x86_64.tar.gz
$: mv elasticsearch-7.4.2 elasticsearch
$: cd elasticsearch
$: ./bin/elasticsearch-plugin install x-pack
```

得到 warning

```
future versions of Elasticsearch will require Java 11; your Java version from [/opt/jdk1.8.0_77/jre] does not meet this requirement
ERROR: this distribution of Elasticsearch contains X-Pack by default
```

1. Elasticsearch 未来需要 Java 11 支持
2. X-Pack 默认已安装

> Elk 日志可视化管理系是目前比较主流的一套日志管理工具。对日志查找，阅读、收集都非常方便。所以今天的正文来了，今天文章的内容写的是 elk 的系统组件 x-pack。一个集安全、警报、监视、报告和图形功能于身的扩展，轻松开启或关闭那你想要的功能。

#### 3.2 修改 JVM 内存参数

可以根据需求修改 `config/jvm.options` 的 `jvm` 内存参数。

> Xm max 需要 4G

```java
Xms1g
Xmm4g
```

#### 3.3 修改监听端口和绑定地址

文件: `config/elasticsearch.yml`

参数: `network.host`

```properties
network.host: 192.168.3.242
```

#### 3.4 启动 ElasticSearch

```
$: bin/elasticsearch
```

#### 3.5证 ElasticSearch 

```bash
curl http://elastic:changeme@192.168.3.242:9200
```

返回

```json
{
  "name" : "vm-ubuntu-201",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "2CeztDYyTJ6XqLr9JRaR3Q",
  "version" : {
    "number" : "7.4.2",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "2f90bbf7b93631e52bafb59b3b049cb44ec25e96",
    "build_date" : "2019-10-28T20:40:44.881551Z",
    "build_snapshot" : false,
    "lucene_version" : "8.2.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```





#### 3.6 启动错误

以下是教程给的解决方案：

* max file descriptors

  * max file descriptors [65535] forelasticsearch process likely too low, increase to at least [65536]

  解决方案: 

  - 修改文件 `/etc/security/limits.conf`，末尾增加如下内容:

  ```bash
  
  ```
* soft nofile 65536
  * hard nofile 65536
  ```
  
  ```
  
* max virtual memory

  * max virtual memory areas vm.max_map_count[65530] likely too low, increase to at least [262144]

  解决方案:

  * 修改文件 `/etc/sysctl.conf`
  * 设置参数 `vm.max_map_count = 262144`
  * 执行命令 `sysctl -p`



实际查阅资料，说是因为部署模式的问题，默认是作为服务器来部署的，而生产环境部署，会对环境的指标进行检查。我们可以通过修改部署模式，来关闭启动检查。

- 修改  `config/elasticsearch.yml`  `discovery.type` 为 `single-node`
- ` discovery.type: single-node`

改为单节点之后，启动正常。

### 4. 安装 Logstash

#### 4.1 下载 Logstash

```bash
$ wget http://pi.file.mpae.cc/%E5%AE%89%E8%A3%85%E5%8C%85/elk/logstash-7.4.2.tar.gz
$ tar -xvf logstash-7.4.2.tar.gz
$ mv logstash-7.4.2 logstash
$ cd logstash 
```

#### 4.2 测试 log

在 logstash 根目录创建配置文件 `sample.conf`

```properties
input {
	stdin {
		type => "syslog"
	}
}

filter {

}

output {
	elasticsearch {
		hosts       => "http://192.168.3.242:9200"
		user        => "elastic"
		password    => "changeme"
	}
	
	stdout {
		codec => rubydebug
	}
}
```



执行`bin/logstash -f sample.conf `

启动之后，输入以下内容:

``` 
Jan 19 12:28:31 elk-test sshd[13990]:pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=sshruser= rhost=92.246.190.31 user=root
```

> 别人测试的内容

返回

```
{
          "host" => "vm-ubuntu-201",
       "message" => "Jan 19 12:28:31 elk-test sshd[13990]:pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=sshruser= rhost=92.246.190.31 user=root",
    "@timestamp" => 2019-11-22T09:55:30.979Z,
      "@version" => "1",
          "type" => "syslog"
}
```



#### 4.3 启动错误

* Logstash 启动缓慢

  * JVM 随机数生成参数配置错误问题

  推荐操作:

  * 修改文件 `$JAVA_HOME/jre/lib/security/java.security`
  * 设置参数 `securerandom.source = file:/dev/urandom`
  * 参考资料
    * https://github.com/elastic/logstash/issues/5491
    * https://docs.oracle.com/cd/E13209_01/wlcp/wlss30/configwlss/jvmrand.html
    * urandom = unblocked random 非堵塞随机数生成
    * 实际按照这个修改后，启动速度依然保持在 1 分钟以上

### 5 安装 Kibana

#### 5.1 下载 Kibana & 安装 x-pack

```
$ wget http://pi.file.mpae.cc/%E5%AE%89%E8%A3%85%E5%8C%85/elk/kibana-7.4.2-linux-x86_64.tar.gz
$ tar -xvf kibana-7.4.2-linux-x86_64.tar.gz
$ cd xxx
$ mv kibana-7.4.2-linux-x86_64 kibana
$ cd kibana
$ ./bin/kibanna-plugin install x-pack
```

> 吐槽为了节省安装，直接把 node_modules 打包到服务里
>
> Plugin installation was unsuccessful due to error "Kibana now contains X-Pack by default, there is no longer any need to install it as it is already present."

#### 5.2 配置访问 IP 和 ElasticSearch 地址

编辑 `config/kibana.yml`，配置 `server.host` 和 `elasticsearch.url`

```properties
# server.host: "localhost"
server.host: "192.168.3.242"

# elasticsearch.url: ["http://localhost:9200"]
elasticsearch.url: ["http://192.168.3.242:9200"]
```

#### 5.3 启动 Kibana

```
$ ./bin/kibana
```

访问服务地址 http://192.168.3.242:5601/app/kibana

默认登录并没有要求我输入密码

#### 5.4 修改中文

修改 `kibana.yml` 中的 `i18n.locale`为 `zh-CN` 。

> 英文弱鸡的福音

#### 5.5 创建 index pattern

1. 进入 Kibana 设置页面， Configure an index pattern
2. Index Pattern 必须和 Logstash output 参数一致，Logstash 输出默认参数为 logstash-*

> 配置的前提是，这个 index 的日志先进入 ElastciSearch 之后，才能在 Kibana 上创建 Index。



### 6 安装 FileBeat

FileBeat 和 Logstash 区别:

- Logstash 兼顾收集日志 & 处理日志
- FileBeat 仅仅收集日志文件，并发送到 Logstash 开放的端口



有个概念是：当你开启 filebeat 程序的时候，它会启动一个或多个探测器（prospectors）去检测你指定的日志目录或文件，对于探测器找出的每一个日志文件，filebeat 启动收割进程（harvester），每一个收割进程读取一个日志文件的新内容，并发送这些新的日志数据到处理程序（spooler），处理程序会集合这些事件，最后 filebeat会发送集合的数据到你指定的地点。

* 检测日志时间间隔： 10s
  * 修改地址: filebeat.yml 中的 # scan_frequency 字段

#### 6.1 下载 & 安装

```bash
$ wget http://pi.file.mpae.cc/%E5%AE%89%E8%A3%85%E5%8C%85/elk/filebeat-7.4.2-linux-x86.tar.gz
$ tar -xf filebeat-7.4.2-linux-x86.tar.gz
$ mv filebeat-7.4.2-linux-x86 filebeat
$ cd filebeat
```

#### 6.2 修改配置

修改的两个点，一个是 log ，配置 log 路径，另个是配置 Logstash 的信息。

#### 6.3 配置修改范例

```properties
filebeat.inputs:

# Below are the input specific configurations.

- type: log

  # Change to true to enable this input configuration.
  enabled: true

  # Paths that should be crawled and fetched. Glob based paths.
  paths:
    - /home/maple/access.log
    #- c:\programdata\elasticsearch\logs\*


#============================= Filebeat modules ===============================

filebeat.config.modules:
  # Glob pattern for configuration loading
  path: ${path.config}/modules.d/*.yml

  # Set to true to enable config reloading
  reload.enabled: false


#==================== Elasticsearch template setting ==========================

setup.template.settings:
  index.number_of_shards: 1
  #index.codec: best_compression
  #_source.enabled: false

#================================ General =====================================

#-------------------------- Elasticsearch output ------------------------------
output.elasticsearch:
  # Array of hosts to connect to.
  hosts: ["192.168.3.242:9200"]

  # Optional protocol and basic auth credentials.
  #protocol: "https"
  #username: "elastic"
  #password: "changeme"

#================================ Processors =====================================

# Configure processors to enhance or manipulate events generated by the beat.

processors:
  - add_host_metadata: ~
  - add_cloud_metadata: ~
```



#### 6.4 重新读取文件

删除执行目录下的 

```
rm ./data/registry/data.json
```



## 测试功能

依次启动 elasticsearch 和 kibana。

```sh
$ cd elasticsearch/
$ ./bin/elasticsearch
...
$ cd kibana
$ ./bin/kibana
```



配置 logstash 的 conf。我这边拿公司业务日志文件来测试，conf 也是前同事改的 conf(具体内容涉及隐私就不贴了)。



```
./filebeat -e -c filebeat.yml
```



---

FileBeat 为了解决 Logstash 开销大的问题。



----

参考：公司某大佬遗留文档 https://souche.yuque.com/koumakan-ancuo/izayoi/ob078w

参考：https://mp.weixin.qq.com/s/eVukCJI-U7uWsoO7uLacUA

