---
date: 2022-2-9 11:23:25
title: "python 对 zip 和 rar 文件进行解压"
tags: [ python, zip, rar ]
---



## 背景

手上有一大堆文件，其中有很多都是压缩文件，但是因为压缩文件并不是在同一个文件夹，所以查找压缩文件夹解压很繁琐。



## 方案

使用 python 的脚本性质，通过遍历所有文件夹，并且使用相对的包来实现对文件的解压。



## package

### zipfile

zip 算法属于公开，所以实现比较简单，加上几乎所有的系统都支持 zip。所以只要安装即可。

```sh
pip install zipfile
```



**存在问题：gbk 文件名乱码**

因为 zipfile 是一个外国人写的，他在编码那块没有考虑其他编码的使用。粗暴的使用`if utf8 else cp437`。所以 gbk 编码的文件名会被转换成 cp437 编码。

有个办法是直接把文件名从 cp437 编码暴力转换成 gbk 编码。然后对文件名进行重命名，但是这个转换可能会导致编码丢失， 不推荐。

简单的办法是，直接修改 zipfile 包。**修改 zipfile.py，直接把两处 `cp437`  替换为 `gbk` 即可**。



解压代码:

```python
zip_file = zipfile.ZipFile(file_name)
for name in zip_file.namelist():
    zip_file.extract(name, folder_name)

zip_file.close()
```



### unrar

rar 解压我使用的是 unrar 包。



**存在问题:  运行报错，依赖库不存在**

> Couldn't find path to unrar library

rar 算法目前依然属于闭源，所以需要依赖 unrarlib 来实现解压。

windows 下需要安装 unrardll.exe <http://www.rarlab.com/rar/UnRARDLL.exe>。然后配置环境变量 `UNRAR_LIB_PATH`  到对应系统（32 位 or 64 位） 的 `UnRaR.dll`。

参考 <https://blog.csdn.net/andiao1218/article/details/101192775>



解压代码:

```python
rar_file = rarfile.RarFile(file_name, mode="r")
rar_file.extract(name, folder_name)
```





## 完整代码

```
import sys, time


class ShowProcess():
    """
    显示处理进度的类
    调用该类相关函数即可实现处理进度的显示
    """
    i = 0  # 当前的处理进度
    max_steps = 0  # 总共需要处理的次数
    max_arrow = 50  # 进度条的长度
    infoDone = 'done'

    # 初始化函数，需要知道总共的处理次数
    def __init__(self, max_steps, infoDone='Done'):
        self.max_steps = max_steps
        self.i = 0
        self.infoDone = infoDone

    # 显示函数，根据当前的处理进度i显示进度
    # 效果为[>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>]100.00%
    def show_process(self, i=None):
        if i is not None:
            self.i = i
        else:
            self.i += 1
        num_arrow = int(self.i * self.max_arrow / self.max_steps)  # 计算显示多少个'>'
        num_line = self.max_arrow - num_arrow  # 计算显示多少个'-'
        percent = self.i * 100.0 / self.max_steps  # 计算完成进度，格式为xx.xx%
        process_bar = '[' + '>' * num_arrow + '-' * num_line + ']' \
                      + '%.2f' % percent + '%' + '\r'  # 带输出的字符串，'\r'表示不换行回到最左边
        sys.stdout.write(process_bar)  # 这两句打印字符到终端
        sys.stdout.flush()
        if self.i >= self.max_steps:
            self.close()

    def close(self):
        print('')
        print(self.infoDone)
        self.i = 0


if __name__ == '__main__':
    max_steps = 100

    process_bar = ShowProcess(max_steps, 'OK')

    for i in range(max_steps):
        process_bar.show_process()
        time.sleep(0.01)

# https://www.cainiaojc.com/note/qag1sz.html
```





```python
import os
import re
import zipfile
from unrar import rarfile
import show_process


def unzip(file_name):
    rm_status = False
    tmp_texts = file_name.split(".")
    tmp_texts.pop()
    folder_name = ".".join(tmp_texts)
    zip_file = None
    try:
        zip_file = zipfile.ZipFile(file_name)
        if os.path.isdir(folder_name):
            pass
        else:
            os.mkdir(folder_name)

        steps = len(zip_file.namelist())
        process_bar = show_process.ShowProcess(steps, "解压成功!")

        for name in zip_file.namelist():
            process_bar.show_process()

            new_file_path = os.path.join(folder_name, name)

            if os.path.exists(new_file_path):
                if not os.path.isdir(new_file_path):
                    os.remove(new_file_path)
                    zip_file.extract(name, folder_name)
                else:
                    try:
                        os.rmdir(new_file_path)
                    except OSError:
                        pass
                    finally:
                        zip_file.extract(name, folder_name)
            else:
                zip_file.extract(name, folder_name)

        rm_status = True
    except Exception:
        rm_status = False
    finally:
        remove_empty_folder(folder_name)
        # zip_file.close()

        if zip_file is not None:
            zip_file.close()

        if rm_status:
            os.remove(file_name)
        else:
            print("@@@@@@@保留文件@@@@@@@@：{}".format(file_name))


def unrar(file_name):
    tmp_texts = file_name.split(".")
    tmp_texts.pop()
    folder_name = ".".join(tmp_texts)
    try:
        rar_file = rarfile.RarFile(file_name, mode="r")
        if os.path.isdir(folder_name):
            pass
        else:
            os.mkdir(folder_name)

        steps = len(rar_file.namelist())
        process_bar = show_process.ShowProcess(steps, "解压成功!")
        namelist = rar_file.namelist()
        for name in namelist:
            process_bar.show_process()
            new_file_path = os.path.join("", folder_name, name)

            if os.path.exists(new_file_path):
                if not os.path.isdir(new_file_path):
                    os.remove(new_file_path)
                    rar_file.extract(name, folder_name)
                else:
                    try:
                        os.rmdir(new_file_path)
                    except OSError:
                        pass
                    finally:
                        rar_file.extract(name, folder_name)
            else:
                rar_file.extract(name, folder_name)
        os.remove(file_name)
    except Exception:
        print("@@@@@@@保留文件@@@@@@@@：{}".format(file_name))
        pass
    finally:
        remove_empty_folder(folder_name)


def remove_empty_folder(dir):
    # print("开始清理路径: {}".format(dir))
    g = os.walk(dir)
    for path, dir_list, file_list in g:
        if len(dir_list) == 0 and len(file_list) == 0:
            print("删除路径: {}".format(path))
            os.rmdir(path)
        else:
            for dir in dir_list:
                remove_empty_folder(os.path.join("", path, dir))


def walk_file(dir_path):
    print("正在处理文件夹 {}".format(dir_path))
    g = os.walk(dir_path)
    for path, dir_list, file_list in g:
        for file in file_list:
            file_path = os.path.join(path, file)
            if re.search(r"\.rar$", file) is not None:
                print("\n------> 匹配到 rar 压缩包: {}".format(file_path))
                # print(file_path)
                unrar(file_path)
                print("\n------> 解压 {} 成功".format(file))
                continue
            elif re.search(r"\.zip$", file) is not None:
                print("\n------> 匹配到 zip 压缩包: {}".format(file_path))
                # print("\"{}\"".format(file_path))
                unzip(file_path)
                print("\n------> 解压 {} 成功".format(file))
                continue

root_path = r"G:\00001"

walk_file(root_path)
```

