---
title: Python 项目 & requirement.txt
tags: python
date: 2022-07-05 17:36:01
---



## 安装依赖

```bash
pip install -r requirement.txt
```



## 环境依赖写入文件

> 是所有的依赖都会写入，不仅仅是项目所依赖的

```
pip freeze > requirements.txt
```



### 仅写入项目所依赖的依赖

```
pip install pipreqs
pipreqs ./
```

