---
title: 廖雪峰 Python 教程笔记
date: 2023-03-16 16:17:59
tags: [python]
---



[教程地址: https://www.liaoxuefeng.com/wiki/1016959663602400](https://www.liaoxuefeng.com/wiki/1016959663602400)

## 生成器和迭代器

```python
# 构建生成器
def odd():
    times = 0
    while True:
        yield times
        times = times + 1
        if times > 10:
            # break
            return 'some value'
# for 循环
for t in odd():
    print(t)

# while 模拟
try:
    generates = odd()
    while True:
        t = next(generates)
        print(t)
except StopIteration as e:
    # 接受函数最后返回的 value
    print(e.value)
```



## 装饰器

```python
def metric(name):
    def decorator(fn):
        # 修正 wrapper 方法的方法名
        @functools.wraps(fn)
        def wrapper(*args, **kw):
            start_time = time.time()
            result = fn(*args, **kw)
            print('%s executed in %s ms' % (name, time.time() - start_time))

            return result
        return wrapper
    return decorator
```



wrapper: 包装纸

decorator: 装潢师；裝修工；粉刷匠



## 偏函数

```python
def max_number(*numbers):
    return max(numbers)


max_number_10 = functools.partial(max_number, 10)

print(max_number_10(1, 2))
```



## Private 变量

```python
class Student(object):
  
    def __init__(self, name, score):
        self.__name = name
        self.__score = score
```



## 类型判断

```python
import types

type(123) == int
type(1.23) == float
type('abc') == str

type(fn)==types.FunctionType
type(abs)==types.BuiltinFunctionType
type(lambda x: x)==types.LambdaType
type((x for x in range(10)))==types.GeneratorType

isinstance(child, super)
```



## 获取对象所有属性和方法

```python
dir('ABC')

hasattr(obj, 'y')
setattr(obj, 'y', 123)
getattr(obj, 'y')
```



## 限定 dict 绑定的字段

```python
form types import MethodType

class Student(object):
    __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称
```





## @property @score.setter

```python
class Student(object):

    @property
    def score(self):
        return self._score

    @score.setter
    def score(self, value):
        if not isinstance(value, int):
            raise ValueError('score must be an integer!')
        if value < 0 or value > 100:
            raise ValueError('score must between 0 ~ 100!')
        self._score = value
```



## 多重继承

```python
class Dog(Mammal, Runnable):
    pass
```



## 类属性

- `__str__`
- `__init__`
- `__repr__`
- `__iter__`
- `__next__`
- `__getitem__`

```python
class Fib(object):
    def __getitem__(self, n):
        if isinstance(n, int): # n 是索引
            a, b = 1, 1
            for x in range(n):
                a, b = b, a + b
            return a
        if isinstance(n, slice): # n 是切片
            start = n.start
            stop = n.stop
            if start is None:
                start = 0
            a, b = 1, 1
            L = []
            for x in range(stop):
                if x >= start:
                    L.append(a)
                a, b = b, a + b
            return L
```

- `__getattr__`

```python
class Student(object):

    def __init__(self):
        self.name = 'Michael'

    def __getattr__(self, attr):
        if attr=='score':
            return 99
          

class Student(object):

    def __getattr__(self, attr):
        if attr=='age':
            return lambda: 25
```

- `__call__`

```python
class Student(object):
    def __init__(self, name):
        self.name = name

    def __call__(self):
        print('My name is %s.' % self.name)
```





## 枚举

遍历成员

```python
for name, member in Month.__members__.items():
    print(name, '=>', member, ',', member.value)
```



```python

from enum import Enum, unique

@unique # 检查是否有重复值
class Weekday(Enum):
    Sun = 0 # Sun的value被设定为0
    Mon = 1
    Tue = 2
    Wed = 3
    Thu = 4
    Fri = 5
    Sat = 6
```



## 动态创建类

```python
Hello = type('Hello', (object,), dict(hello=fn)) # 创建 Hello class
```

1. class 的名称；
2. 继承的父类集合，注意 Python 支持多重继承，如果只有一个父类，别忘了 tuple 的单元素写法；
3. class的方法名称与函数绑定，这里我们把函数`fn`绑定到方法名`hello`上。



## metaclass



```python
# metaclass是类的模板，所以必须从`type`类型派生：
class ListMetaclass(type):
    def __new__(cls, name, bases, attrs):
        attrs['add'] = lambda self, value: self.append(value)
        return type.__new__(cls, name, bases, attrs)
      
class MyList(list, metaclass=ListMetaclass):
    pass
```

`__new__()`方法接收到的参数依次是：

1. 当前准备创建的类的对象；
2. 类的名字；
3. 类继承的父类集合；
4. 类的方法集合。



## StringIO ，BytesIO

```
from io import StringIO, BytesIO
```



## os

```python
import os

os.name
os.uname()
os.environ.get("NODE_ENV")
os.path.abspath(".")
os.path.join("/home", "xx")
os.mkdir()
os.rmdir()
os.path.split("/tmp/t.log") # -> ("/tmp", "t.log")
os.path.splitext() # -> ("/tmp/t", ".log")
os.rename()
os.remove()
os.path.isdir()
os.path.isfile()

```



## 序列化

```python
import pickle
import json

pickle.dump()
pickle.load()

json.dumps()
json.loads()

# 对象格式化成 JSON
def student2dict(std):
    return {
        'name': std.name,
        'age': std.age,
        'score': std.score
    }
print(json.dumps(s, default=student2dict))

# JSON 格式化成对象
def dict2student(d):
    return Student(d['name'], d['age'], d['score'])
print(json.loads(json_str, object_hook=dict2student))

```



## 多进程

### fork

```python
import os

import os

print('Process (%s) start...' % os.getpid())
# Only works on Unix/Linux/Mac:
pid = os.fork()
pid_2 = os.fork()
if pid == 0 or pid_2 == 0:
    print('I am child process (%s) and my parent is %s.' % (os.getpid(), os.getppid()))
else:
    print('I (%s) just created a child process (%s).' % (os.getpid(), pid))

# Process (43784) start...
# I (43784) just created a child process (43785).
# I am child process (43785) and my parent is 43784.
# I am child process (43786) and my parent is 43784.
# I am child process (43787) and my parent is 43785.
```



### multiprocessing 

> 跨平台，支持 Windows

```python
from multiprocessing import Process
import os


# 子进程要执行的代码
def run_proc(name):
    print('Run child process %s (%s)...' % (name, os.getpid()))


if __name__ == '__main__':
    print('Parent process %s.' % os.getpid())
    p = Process(target=run_proc, args=('test',))
    print('Child process will start.')
    p.start()

    p.join()
    print('Child process end.')

    np = Process(target=run_proc, args=('new_test', ))
    np.start()
    np.join()

    print('Child process end again')
```

### multiprocessing.Pool

```python
from multiprocessing import Pool

p = Pool(4) # 初始化进程数最大为 4 的进程池
for i in range(5):
	p.apply_async(task, args = (i, ))
 
p.close()
p.join()
```



### subprocess

```python
import subprocess

print('$ nslookup www.python.org')
r = subprocess.call(['nslookup', 'www.python.org'])
print('Exit code:', r)
```



communicate()

```python
import subprocess

print('$ nslookup')
p = subprocess.Popen(['nslookup'], stdin=subprocess.PIPE, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
output, err = p.communicate(b'set q=mx\npython.org\nexit\n')
print(output.decode('utf-8'))
print('Exit code:', p.returncode)
```



### Queue

```python
from multiprocessing import Process, Queue
import os, time, random

# 写数据进程执行的代码:
def write(q):
    print('Process to write: %s' % os.getpid())
    for value in ['A', 'B', 'C']:
        print('Put %s to queue...' % value)
        q.put(value)
        time.sleep(random.random())

# 读数据进程执行的代码:
def read(q):
    print('Process to read: %s' % os.getpid())
    while True:
        value = q.get(True)
        print('Get %s from queue.' % value)

if __name__=='__main__':
    # 父进程创建Queue，并传给各个子进程：
    q = Queue()
    pw = Process(target=write, args=(q,))
    pr = Process(target=read, args=(q,))
    # 启动子进程pw，写入:
    pw.start()
    # 启动子进程pr，读取:
    pr.start()
    # 等待pw结束:
    pw.join()
    # pr进程里是死循环，无法等待其结束，只能强行终止:
    pr.terminate()
```



```python
from multiprocessing import Process, Queue

q = Queue()

q.put("value")
v = q.get(True)

print(v)
```





## 多线程

```python
import time, threading

# 新线程执行的代码:
def loop():
    print('thread %s is running...' % threading.current_thread().name)
    n = 0
    while n < 5:
        n = n + 1
        print('thread %s >>> %s' % (threading.current_thread().name, n))
        time.sleep(1)
    print('thread %s ended.' % threading.current_thread().name)

print('thread %s is running...' % threading.current_thread().name)
t = threading.Thread(target=loop, name='LoopThread')
t.start()
t.join()
print('thread %s ended.' % threading.current_thread().name)
```

- threading.curent_thread()
- Tread()
  - start()
  - join()



### 线程锁

```python
import threading

lock = threading.Lock()

lock.acquire()
# do someting
lock.release()
```



### GIL 全局锁

多线程最多只能利用一个进程。

GIL 全局锁相当于给所有线程上锁。导致线程运行需要获取锁。



## 线程变量

- 线程变量只有线程可以看到访问
- 全局变量修改必须加锁，否则会被不同的线程同时修改
- 通过设置全局变量 dict[threading.current_thread()] 来实现线程与变量对象的绑定



```python
import threading

local_params = threading.local()
```



## 进程和线程对比

区别：

|          | 进程                         | 线程                                   |
| -------- | ---------------------------- | -------------------------------------- |
| 共享内存 | 不支持                       | 支持                                   |
| 性能     | 相对较好                     | 相对较差                               |
| 稳定性   | 挂掉不影响主进程             | 挂掉之后影响当前线程所在的主进程       |
| 创建成本 | 较高，且系统支持的进程数有限 | 一个进程可以创建融甘线程，创建成本较低 |



总结：

- 对于  python 这种运行性能较差的脚本语言，一般不建议作为计算密集型服务
- 而对于 IO 密集型服务，多线程 / 进程可以解决进程等待问题，但不可避免导致在线程切换的情况造成性能损耗。
- 异步  IO 是解决问题的趋势



## 分布式进程

```python
# task_master.py

import random, time, queue
from multiprocessing.managers import BaseManager

task_queue = queue.Queue()
result_queue = queue.Queue()


# 发送任务的队列:
def get_task_queue():
    return task_queue


# 接收结果的队列:
def get_result_queue():
    return result_queue


# 从BaseManager继承的QueueManager:
class QueueManager(BaseManager):
    pass


# 把两个Queue都注册到网络上, callable参数关联了Queue对象:
QueueManager.register('get_task_queue', callable=get_task_queue)
QueueManager.register('get_result_queue', callable=get_result_queue)
# 绑定端口5000, 设置验证码'abc':
manager = QueueManager(address=('', 5000), authkey=b'abc')

if __name__ == '__main__':
    # 启动Queue:
    manager.start()
    # 获得通过网络访问的Queue对象:
    task = manager.get_task_queue()
    result = manager.get_result_queue()

    # 放几个任务进去:
    for i in range(10):
        n = random.randint(0, 10000)
        print('Put task %d...' % n)
        task.put(n)

    # 从result队列读取结果:
    print('Try get results...')

    times = 0

    while True:
        try:
            r = result.get(timeout=10)
            print('Result: %s' % r)
            times = times + 1
            if times >= 10:
                break
        except queue.Empty:
            print('Empty Result')
            pass

    # for i in range(10):
    #     r = result.get(timeout=10)
    #     print('Result: %s' % r)
    # 关闭:
    manager.shutdown()
    print('master exit.')
```



```python
# task_worker.py

import time, sys, queue
from multiprocessing.managers import BaseManager


# 创建类似的QueueManager:
class QueueManager(BaseManager):
    pass


# 由于这个QueueManager只从网络上获取Queue，所以注册时只提供名字:
QueueManager.register('get_task_queue')
QueueManager.register('get_result_queue')

# 连接到服务器，也就是运行task_master.py的机器:
server_addr = '127.0.0.1'
print('Connect to server %s...' % server_addr)
# 端口和验证码注意保持与task_master.py设置的完全一致:
queue_manager = QueueManager(address=(server_addr, 5000), authkey=b'abc')
# 从网络连接:
queue_manager.connect()

# 获取Queue的对象:
task = queue_manager.get_task_queue()
result = queue_manager.get_result_queue()

# 从task队列取任务,并把结果写入result队列:
for i in range(10):
    try:
        n = task.get(timeout=1)
        print('run task %d * %d...' % (n, n))
        r = '%d * %d = %d' % (n, n, n * n)
        time.sleep(1)
        result.put(r)
    except queue.Empty:
        print('task queue is empty.')
# 处理结束:
print('worker exit.')
```



## datetime

```python
from datetime import datetime, timedelta, timezone


def main():
    now = datetime.now()
    print(now)

    date = datetime(2023, 3, 23, 16, 56, 0, 123456)
    print(date)

    ts = now.timestamp()
    print(ts)

    _now = datetime.fromtimestamp(ts)
    print(_now)

    utc_date = datetime.utcfromtimestamp(ts)
    print(utc_date)

    date_str = now.strftime("%Y-%m-%d %H:%M:%S")
    print(date_str)

    format_date_str = datetime.strptime("2023-3-23 16:56:0", "%Y-%m-%d %H:%M:%S")
    print(format_date_str)

    utc_dt = datetime.utcnow().replace(tzinfo=timezone.utc)
    print(utc_dt)

    bj_dt = datetime.utcnow().astimezone(timezone(timedelta(hours=8)))
    print(bj_dt)

    dj_dt = bj_dt.astimezone(timezone(timedelta(hours=9)))
    print(dj_dt)


if __name__ == '__main__':
    main()

```





## collections

### namedtuple

定义名称、字段和个数的元组

```python
Point = namedtuple("Point", ["x", "y"])
p = Point(1,2)
p.x # 1
p.y # 2
```



### deque

队列，快速插入和删除元素

```python
q = deque(["a", "b", "c"])
q.append('x')
q.appendleft('x')
q.pop()
q.popleft()
```



### defaultdict

默认值

```python
from collections import defaultdict
dd = defaultdict(lambda: 'N/A')
```



### OrderedDict

字段 key 有序。（按照插入顺序）。



### ChainMap

链接多个 dict，并按照优先级返回



### Counter

计数器

```python
c = Counter()
    for ch in 'programming':
        c[ch] = c[ch] + 1

    print(c)

    c.update("abc")

    print(c)
```





## argparse

参数解析

```python
# backup.py

import argparse

def main():
    # 定义一个ArgumentParser实例:
    parser = argparse.ArgumentParser(
        prog='backup', # 程序名
        description='Backup MySQL database.', # 描述
        epilog='Copyright(r), 2023' # 说明信息
    )
    # 定义位置参数:
    parser.add_argument('outfile')
    # 定义关键字参数:
    parser.add_argument('--host', default='localhost')
    # 此参数必须为int类型:
    parser.add_argument('--port', default='3306', type=int)
    # 允许用户输入简写的-u:
    parser.add_argument('-u', '--user', required=True)
    parser.add_argument('-p', '--password', required=True)
    parser.add_argument('--database', required=True)
    # gz参数不跟参数值，因此指定action='store_true'，意思是出现-gz表示True:
    parser.add_argument('-gz', '--gzcompress', action='store_true', required=False, help='Compress backup files by gz.')


    # 解析参数:
    args = parser.parse_args()

    # 打印参数:
    print('parsed args:')
    print(f'outfile = {args.outfile}')
    print(f'host = {args.host}')
    print(f'port = {args.port}')
    print(f'user = {args.user}')
    print(f'password = {args.password}')
    print(f'database = {args.database}')
    print(f'gzcompress = {args.gzcompress}')

if __name__ == '__main__':
    main()
```



## base64

```python
import base64
>>> base64.b64encode(b'binary\x00string')
b'YmluYXJ5AHN0cmluZw=='
>>> base64.b64decode(b'YmluYXJ5AHN0cmluZw==')
b'binary\x00string'
>>> base64.urlsafe_b64encode(b'i\xb7\x1d\xfb\xef\xff')
b'abcd--__'
>>> base64.urlsafe_b64decode('abcd--__')
b'i\xb7\x1d\xfb\xef\xff'
```



## struct

字节处理



## hashlib

```python
import hashlib

md5 = hashlib.md5()

# sha1 = hashlib.sha1()

md5.update('how to use md5 in python hashlib?'.encode('utf-8'))
md5.update('python hashlib?'.encode('utf-8'))
print(md5.hexdigest())

```





## hmac

```python
import hmac
message = b'Hello, world!'
key = b'secret'
h = hmac.new(key, message, digestmod='MD5')
# 如果消息很长，可以多次调用h.update(msg)
h.hexdigest()
>> 'fa4ee7d173f2d97ee79022d1a7355bcf'
```



## itertools

迭代对象工具

```python
# 无限迭代器
itertools.count(1) # 1, 2, 3
itertools.cycle('abc') # 'a', 'b','c','a'
itertools.repeat('ABC', 3) # 'ABC', 'ABC'

# 串联迭代器
itertools.chain(iters_a, iters_b)

# group by
itertools.groupby()
```





## contextlib

封装支持 with 函数的方法



```python
@contextmanager
def create_query(name):
    print('Begin')
    q = Query(name) # 创建资源
    yield q # 返回资源
    print('End') # 释放资源
    

with create_query(name) as q:
  	# do something
```







## urllib



## XML



## HTMLParser

