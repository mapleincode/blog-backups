---
date: 2022-05-19 14:48:15
title: python venv
tags: [ python ]
---

venv 是 pyhton 的虚拟环境



## 安装

```sh
sudo apt install python3.8-venv
```



## 使用

```shell
python3 -m venv pgadmin4 			# 创建 venv
source pgadmin4/bin/activate 	# 激活（进入） venv
# ...													# do something
deactivate										# 退出 venv
```

