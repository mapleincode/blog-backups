---
date: 2019-11-11 10:09:46
tags: [Shell]
title: Shell 自动输入 sudo 密码
---

1. 使用 echo 和管道命令

```
#!/bin/bash
echo password | sudo -S service runtime* status
```

2. 使用文本块输入重定向

> 发现这个命令不能用在 shell 的判断条件里

```shell
#!/bin/bash
sudo -S service talend-runtime* status << EOF
password
EOF
```



> 第一种方法是 sudo 函数独有，但是输入重定向，对其他需要输入密码的函数也会有效



参考:

1. https://blog.csdn.net/qiyueqinglian/article/details/48290905