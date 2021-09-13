---
date: 2020-8-13 15:07:55
title: Python & Ubuntu
tags: [ python, ubuntu ]
---

## 关于 Python3 和 Python2

相当于两个版本，一般 linux 上预装的都是 python2 ，也不排除预装 python3。目前 linux 在以把 python2 依赖减少以达到放弃。



## 安装 Python3

安装 python3

```
sudo apt install python3
```

安装 pip3

```
sudo apt install python3-pip
```



> 一般 pip 和 pip2 指的都是 python2-pip



## pip3 安装依赖

```
pip3 install flask
```

这里重要一点是，安装时不要加 sudo。否则会把依赖安装至系统目录而不是用户目录



## pm2 启动 python 应用

对于 python2 直接启动即可

```
pm2 start app.py
```



对于指定 python3 应用

```
pm2 start app.py -x -x --interpreter python
```

