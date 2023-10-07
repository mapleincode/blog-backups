---
title: Python with as 的作用
date: 2018-08-29 18:01:32
tags: [ python ]
---
# Python with as 的作用

这几天在入门 Python，[https://learnxinyminutes.com/docs/python3/](https://learnxinyminutes.com/docs/python3/)，看到 `with as`，这边说了一句

> \# Instead of try/finally to cleanup resources you can use a with statement
>
> 如果不使用 try/finally 来清理资源，你可以使用声明这个功能。



```py
with open("1.txt") as f
	for line in f:
		print(line)
```

首先说下 with as 的特点:

1. 结束操作之后不需要再调用一次函数
2. Error 捕捉。

## 实现方式

对于可以使用`with as` 的对象必须满足两个条件:

1. 拥有 `__enter__`方法
2. 拥有`__exit__`方法

前者是实行操作的方法，后者是退出操作的方法。

借鉴网上的一个 DEMO:



```python
class Sample:
	def __enter__(self):
		return self;
		
	def __exit__(self, type, value, trace):
		print("type " + type)
         print("value " + value)
         print("trace " + trace)
        def do_something(self):
            return 2048
with Sample() as sample:
    sample.do_something()
```

这里要注意的是 `__exit__`函数除了self 的其他三个参数。

分别是 Error 的 type、value 和 trace-stack。

`__exit__`函数可以作为操作的 finally 函数，比如资源清理、文件关闭、错误恢复等一系列操作。




