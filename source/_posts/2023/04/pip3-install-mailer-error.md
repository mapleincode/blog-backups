---
title: Python3 安装 mailer 失败
date: 2023-04-20 19:37:30
tags: [python3]
---



from: https://bobbyhadz.com/blog/python-error-in-package-setup-command-use-2to3-is-invalid



原因：

mailer 是 Python2 的包。在 Python3 环境安装会因为 setuptools 默认版本过高，导致安装失败。

解决方案：

安装低版本 setuptools

```
pip install --upgrade pip "setuptools<58.0"
```

