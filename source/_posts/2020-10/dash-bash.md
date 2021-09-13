---
title: "set: Illegal option -o pipefail;ubuntu 执行 bash 脚本发生报错"
date: 2020-10-14 14:43:48
tags: [ ubuntu ]
---

**造成原因，Debian 和 Ubuntu 的 /bin/sh 指向的是 dash 而不是 bash。**

- Dash Shell 比 Bash Shell 小的多，符合POSIX标准。
- Dash 支持的命令比 Bash 要小
-  查看状态: **ls /bin/sh -al**
- 重新配置: **udo dpkg-reconfigure dash**



区别主要有

```
1.定义函数

bash: function在bash中为关键字

dash: dash中没有function这个关键字

2.select var in list; do command; done

bash:支持

dash:不支持, 替代方法:采用while+read+case来实现

3. echo {0..10}

bash:支持{n..m}展开

dash:不支持，替代方法, 采用seq外部命令

4. here string

bash:支持here string

dash:不支持, 替代方法:可采用here documents

5. >&word重定向标准输出和标准错误

bash: 当word为非数字时，>&word变成重定向标准错误和标准输出到文件word

dash: >&word, word不支持非数字, 替代方法: >word 2>&1; 常见用法 >/dev/null 2>&1

6. 数组

bash: 支持数组, bash4支持关联数组

dash: 不支持数组，替代方法, 采用变量名+序号来实现类似的效果

7. 子字符串扩展

bash: 支持${parameter:offset:length},${parameter:offset}

dash: 不支持， 替代方法:采用expr或cut外部命令代替

8. 大小写转换

bash: 支持${parameter^pattern},${parameter^^pattern},${parameter,pattern},${parameter,,pattern}

dash: 不支持，替代方法:采用tr/sed/awk等外部命令转换

9. 进程替换<(command), >(command)

bash: 支持进程替换

dash: 不支持, 替代方法, 通过临时文件中转

10. [ string1 = string2 ] 和 [ string1 == string2 ]

bash: 支持两者

dash: 只支持=

11. [[ 加强版test

bash: 支持[[ ]], 可实现正则匹配等强大功能

dash: 不支持[[ ]], 替代方法，采用外部命令

12. for (( expr1 ; expr2 ; expr3 )) ; do list ; done

bash: 支持C语言格式的for循环

dash: 不支持该格式的for, 替代方法，用while+$((expression))实现

13. let命令和((expression))

bash: 有内置命令let, 也支持((expression))方式

dash: 不支持，替代方法，采用$((expression))或者外部命令做计算

14. $((expression))

bash: 支持id++,id--,++id,--id这样到表达式

dash: 不支持++,--, 替代方法:id+=1,id-=1, id=id+1,id=id-1

```





作者：无言89

链接：https://www.jianshu.com/p/762d4cccee7e

来源：简书

著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。