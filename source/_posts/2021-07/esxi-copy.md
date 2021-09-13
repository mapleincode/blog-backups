---
date: 2021-07-21 11:45:34
tags: [ esxi ]
title: ESXi 支持复制粘贴
---

## 修改办法

设置 - 高级 - 编辑配置

添加

```
isolation.tools.copy.disable FALSE
isolation.tools.paste.disable FALSE
```



## 其他

```
isolation.tools.dnd.disable  禁用拖放(drag n drop)
isolation.tools.hgfs.disable = FALSE 共享文件夹
```



## Warining

注意 vmware tools 的状态

