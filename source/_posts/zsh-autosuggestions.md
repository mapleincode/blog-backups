---
title: oh-my-zsh 安装 zsh-autosuggestions 插件
date: 2018-03-18 03:29:13
tags: oh-my-zsh
---

zsh-autosuggestions 是 oh-my-zsh 的一个试用插件，可以实现动态显示历史输入记录的效果。

![](https://camo.githubusercontent.com/8135e25b744f29e5fd83964eded4bd255aa1da74/68747470733a2f2f61736369696e656d612e6f72672f612f33373339302e706e67)

和 oh-my-zsh 一般的内置插件不一样，不能通过光改动`~/.zshrc`来启动，需要额外配置。

>   树莓派上安装这个插件，bash 输入变得十分卡顿，因此对于性能较差的机子(尤其是硬盘速度)，不建议使用这个插件。

## 安装插件

从 GitHub 上 clone 插件。

```sh
git clone git://github.com/zsh-users/zsh-autosuggestions ~/.zsh/zsh-autosuggestions
```

## 启动插件

```sh
source ~/.zsh/zsh-autosuggestions/zsh-autosuggestions.zsh
```

> 可以把这句话添加到`~/.zshrc`文件末端


