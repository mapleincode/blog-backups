---
title: MC 服务器启动之后无法在服务端输入指令
date: 2019-02-25 15:21:42
tags: [ MineCraft, Java ]
---



不久前，发现 MC 服务器居然不能输入指令关闭服务器，在启动过程中有报错。

```java
java.io.IOException: Resource temporarily unavailable
	at java.io.FileInputStream.readBytes(Native Method)
	at java.io.FileInputStream.read(FileInputStream.java:272)
	at java.io.BufferedInputStream.fill(BufferedInputStream.java:235)
	at java.io.BufferedInputStream.read(BufferedInputStream.java:254)
	at java.io.FilterInputStream.read(FilterInputStream.java:83)
	at org.bukkit.craftbukkit.libs.jline.console.ConsoleReader$1.read(ConsoleReader.java:167)
	at org.bukkit.craftbukkit.libs.jline.internal.InputStreamReader.read(InputStreamReader.java:267)
	at org.bukkit.craftbukkit.libs.jline.internal.InputStreamReader.read(InputStreamReader.java:204)
	at org.bukkit.craftbukkit.libs.jline.console.ConsoleReader.readCharacter(ConsoleReader.java:995)
	at org.bukkit.craftbukkit.libs.jline.console.ConsoleReader.readLine(ConsoleReader.java:1167)
	at net.minecraft.server.dedicated.DedicatedServerCommandThread.run(DedicatedServerCommandThread.java:38)
```

查询到问题的原因是不久前升级的 Ubuntu 内核导致，附上原文：

All,

This is a message to let you know that Ubuntu has recently pushed what appears to be a bad kernel update to Ubuntu 18.04 users.

If you are using Ubuntu 18.04, please note that the following kernel appears to have issues handling console input: 

4.15.0-44-generic #47-Ubuntu SMP Mon Jan 14 11:26:59 UTC 2019 

Older (e.g.  4.15.0-43-generic #46-Ubuntu SMP Thu Dec 6 14:45:28 UTC 2018 ) kernels are not affected.

If you are using this kernel and have a message along the lines of "java.io.IOException: Resource temporarily unavailable", this is the reason.

 We are still investigating the exact cause / reason for this.



If you are affected by this issue there are a number of workarounds available (you only need to use one).

1. Spigot / BungeeCord: Add -Djline.terminal=jline.UnsupportedTerminal to your Java arguments
2. Spigot: Add --nojline to your Spigot arguments
3. Reboot your server and select 4.15.0-43, or any other non broken Linux kernel.
4. Install a non broken kernel: apt install linux-image-4.15.0-43-generic, remove the broken one: apt remove linux-image-4.15.0-44-generic, and reboot.

Please direct any queries to the 1.13.2 discussion thread, or general forums:



https://www.spigotmc.org/threads/344189/



EDIT: Please see the below post for updates.



[原文地址](https://www.spigotmc.org/threads/ubuntu-18-04-bad-kernel-update.357871/)