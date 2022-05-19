---
date: 2022-05-19 14:54:49
tags: [python pip]
title: python 更换国内镜像源
---

## 临时修改

清华源

> 清华大学的 pip 源，它是官网 pypi 的镜像，每隔5分钟同步一次，推荐使用。

```shell
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple \
    --trusted-host pypi.tuna.tsinghua.edu.cn packagename
```



豆瓣源

```shell
pip install -i http://pypi.douban.com/simple \
    --trusted-host pypi.douban.com packagename
```


阿里源

```shell
pip install -i http://mirrors.aliyun.com/pypi/simple \
    --trusted-host mirrors.aliyun.com/pypi/simple/ packagename
```



## 永久修改

修改 `~/.pip/pip.conf` 文件，如果没有就创建一个，写入如下内容（以清华源为例）：

```shell
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host = pypi.tuna.tsinghua.edu.cn
```



————————————————
版权声明：本文为CSDN博主「zuimrs」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/zuimrs/article/details/79774781

