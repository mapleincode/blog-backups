---
title: Ubuntu 安装 Node.js 环境
date: 2018-02-21 18:29:17
tags: [ Node.js, Linux ]
---

## 官方编译版本安装

使用官方预先编译好的二进制安装包进行安装。

从`https://nodejs.org/en/download/`选择对应的版本的二进制包，下载到 Linux 上。

```shell
# 下载
wget https://nodejs.org/dist/{version}/node-{version}-linux-x64.tar.xz
# 解压
tar -xvf node-{version}-linux-x64.tar.xz
# 进入文件夹
cd node-{version}-linux-x64/
# 删除说明文件 & 证书 & changelog
rm LICENSE README.md CHANGELOG.md
# 复制文件到系统目录
(sudo) cp -r * /usr/local/
# 配置 PATH 环境到本目录
# export PATH=$PWD/bin:$PATH
```

## 源代码安装

下载源代码编译安装。

```shell
# 下载源代码
wget https://nodejs.org/dist/{version}/node-{version}.tar.gz
# 解压
tar -xvf node-{version}.tar.gz
# 进入文件夹
cd node-{version}
# 生成配置文件
./configure
# 编译
make -j{编译核心数}
# 安装
(sudo) make install
```

编译需要安装依赖

```shell
# python
(sudo) apt install python
# make
(sudo) apt install make
# g++
(sudo) apt install g++
```

单核编译大概需要一个小时的时间，当然如果核心比较多，编译的速度也会大大加快。

> P.S. 树莓派编译大概需要两个小时多。 (version:Raspberry 2B)

## NVM 安装

NVM 是一个脚本，用于 node 版本安装和切换。

官网地址: [http://nvm.sh](http://nvm.sh/)

### 安装 NVM

官方提供了 `curl` 和`wget`两个方式安装

```shell
# curl
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
# wget
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
```

安装完成之后，脚本会自动将启动脚本写入`.bashrc`、`.zshrc`中。

```shell
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```

默认需要 `source .bashrc` 或者重新打开 Shell 让 `nvm` 命令可以被使用。

```shell
# 帮助命令
nvm --help
# 下载安装 verison 8 的最新版本 node
nvm install 8
# 切换 node 版本
nvm use 8
```

然后 Node.js 环境就安装完毕了

```shell
$ node -v
v8.9.0
```

### 修改下载源

默认 NVM 会从官网拉取镜像，速度十分感人，所以需要修改下载源。

NVM 提供了一个环境变量用来修改下载镜像地址。

```shell
export NVM_NODEJS_ORG_MIRROR=http://npm.taobao.org/mirrors/node
```

可以把环境变量修改写入`.bashrc`或者`.zshrc`就可以保证一直修改了。

> 如果是写入 Shell 配置文件需要`source .bashrc` 才能生效



### 使用 NVM 的优点

1. 方便版本管理

   很多人需要在公司的代码版本和自己研究的新版本之间切换，而  NVM 为他们提供了便利

2. 文件更加统一

   NVM 会将所有文件都安装到  `~/.nvm`这个目录下，包括`-g`安装的全局函数。这样安装 Node.js 就不会把文件污染`/usr/local`目录，安装全局应用也不需要 root 权限。如果不要 node 环境，直接删除`~/.nvm`即可。

### ROOT 账户

因为 NVM 是通过`$PATH`来实现 Node 环境的切换，而启动命令则写在 Shell 配置文件，所以默认 root 用户是没有配置 NVM 的。如果需要 root 用户也有 node 环境，那就只能在 root 用户目录下的 `.bashrc`文件里也添加启动脚本。

或者，在启动程序之前，手动将 Node 的路径添加到`$PATH`。



