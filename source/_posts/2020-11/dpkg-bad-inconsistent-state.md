---
date: 2020-11-23 14:48:42
title: Dpkg 包损坏无法安装 & 卸载
---



卸载时报错：

```
package is in a very bad inconsistent state;
you should reinstall it before attempting a removal
```



解决方法：

> 强制卸载



```
 sudo dpkg --remove --force-remove-reinstreq xxxxxx
```

