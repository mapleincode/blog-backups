---
title: Python shutil 笔记
date: 2023-09-20 10:44:22
tag: [python]
---



## 目录和文件操作



### shutil.**copyfile**(*src*, *dst*, \*, *follow_symlinks=True*)



将名为 *src* 的文件的内容（不包括元数据）拷贝到名为 *dst* 的文件并以尽可能高效的方式返回 *dst*。 *src* 和 *dst* 均为路径类对象或以字符串形式给出的路径名。

*dst* 必须是完整的目标文件名；



### *exception* shutil.**SameFileError**

此异常会在 copyfile() 中的源和目标为同一文件时被引发。



### shutil.**copymode**(*src*, *dst*, *, *follow_symlinks=True*)

从 *src* 拷贝权限位到 *dst*。 文件的内容、所有者和分组将不受影响。 



###  shutil.**copystat**(*src*, *dst*, *, *follow_symlinks=True*)

从 *src* 拷贝权限位、最近访问时间、最近修改时间以及旗标到 *dst*。 在 Linux上，copystat() 还会在可能的情况下拷贝“扩展属性”。 文件的内容、所有者和分组将不受影响。 *src* 和 *dst* 均为路径类对象或字符串形式的路径名。

> copystat 与 copymode 相比，除了权限位之外，还多复制了访问时间、修改时间和旗标



#### shutil.**copy**(*src*, *dst*, *, *follow_symlinks=True*)

将文件 *src* 拷贝到文件或目录 *dst*。 *src* 和 *dst* 应为 路径类对象 或字符串。 如果 *dst* 指定了一个目录，文件将使用 *src* 中的基准文件名拷贝到 *dst* 中。 如果 *dst* 指定了一个已存在的文件，它将被替换。 返回新创建文件所对应的路径。

如果 *follow_symlinks* 为 `False` 且 *src* 为符号链接，则 *dst* 也将被创建为符号链接。

 如果 *follow_symlinks* 为`True`且 *src* 为符号链接，*dst* 将成为 *src* 所指向的文件的一个副本。

copy() 会拷贝文件数据和文件的权限模式 (参见 os.chmod())。 其他元数据，例如文件的创建和修改时间不会被保留。 要保留所有原有的元数据，请改用 copy2() 。

#### shutil.**copy2**(*src*, *dst*, *, *follow_symlinks=True*)

类似于 [`copy()`](https://docs.python.org/zh-cn/3/library/shutil.html#shutil.copy)，区别在于 [`copy2()`](https://docs.python.org/zh-cn/3/library/shutil.html#shutil.copy2) 还会尝试保留文件的元数据。



> copy() 赋值权限但是不拷贝创建修改时间等元数据
>
> copy2() 尽量保留所有源数据（但是不是绝对）
>
> 而 copyfile() 只复制源文件的文件内容，而不涉及任何元数据复制



#### shutil.ignore_patterns(**patterns*)

这个工厂函数会创建一个函数，它可被用作 copytree() 的 ignore 可调用对象参数，以忽略那些匹配所提供的 glob 风格的 patterns 之一的文件和目录。 参见以下示例。



#### shutil.copytree(src, dst, symlinks=False, ignore=None, copy_function=copy2, ignore_dangling_symlinks=False, dirs_exist_ok=False)

递归地将以 src 为根起点的整个目录树拷贝到名为 dst 的目录并返回目标目录。

 所需的包含 dst 的中间目录在默认情况下也将被创建。

目录的权限和时间会通过 *copystat()* 来拷贝，单个文件则会使用 *copy2()* 来拷贝。

如果 symlinks 为 True，源目录树中的符号链接会在新目录树中表示为符号链接，并且原链接的元数据在平台允许的情况下也会被拷贝；如果为 False 或省略，则会将被链接文件的内容和元数据拷贝到新目录树。

当 symlinks 为 True 时，如果符号链接所指向的文件不存在，则会在拷贝进程的末尾将一个异常添加到 Error 异常中的错误列表。 如果你希望屏蔽此异常那就将可选的 ignore_dangling_symlinks 旗标设为真值。 请注意此选项在不支持 os.symlink() 的平台上将不起作用。

如果给出了 ignore，它必须是一个可调用对象，该对象将接受 copytree() 所访问的目录以及 os.listdir() 所返回的目录内容列表作为其参数。 由于 copytree() 是递归地被调用的，ignore 可调用对象对于每个被拷贝目录都将被调用一次。 该可调用对象必须返回一个相对于当前目录的目录和文件名序列（即其第二个参数的子集）；随后这些名称将在拷贝进程中被忽略。 ignore_patterns() 可被用于创建这种基于 glob 风格模式来忽略特定名称的可调用对象。



#### shutil.**rmtree**(*path*, *ignore_errors=False*, *onerror=None*, ***, *dir_fd=None*)

Delete an entire directory tree; 

*path* must point to a directory (but not a symbolic link to a directory). 

If *ignore_errors* is true, errors resulting from failed removals will be ignored; 

if false or omitted, such errors are handled by calling a handler specified by *onerror* or, if that is omitted, they raise an exception.



> 备注 在支持必要的基于 fd 的函数的平台上，默认会使用 rmtree() 的可防御符号链接攻击的版本。 在其他平台上，rmtree() 较易遭受符号链接攻击：给定适当的时间和环境，攻击者可以操纵文件系统中的符号链接来删除他们在其他情况下无法访问的文件。 应用程序可以使用 rmtree.avoids_symlink_attacks 函数属性来确定此类情况具体是哪一些。



#### shutil.move(*src, dst, copy_function=copy2*)

递归地将一个文件或目录 (src) 移至另一位置 (dst) 并返回目标位置。

如果目标是已存在的目录，则 src 会被移至该目录下。 如果目标已存在但不是目录，它可能会被覆盖，具体取决于 os.rename() 的语义。

如果目标是在当前文件系统中，则会使用 os.rename()。 在其他情况下，src 将被拷贝至 dst，使用的函数为 copy_function，然后目标会被移除。 对于符号链接，则将在 dst 之下或以其本身为名称创建一个指向 src 目标的新符号链接，并且 src 将被移除。

如果给出了 copy_function，则它必须为接受两个参数 src 和 dst 的可调用对象，并将在 os.rename() 无法使用时被用来将 src 拷贝到 dst。 如果源是一个目录，则会调用 copytree()，并向它传入 copy_function。 默认的 copy_function 是 copy2()。 使用 copy() 作为 copy_function 允许在无法附带拷贝元数据时让移动操作成功执行，但其代价是不拷贝任何元数据。



#### shutil.disk_usage(*path*)

返回给定路径的磁盘使用统计数据，形式为一个 named tuple，其中包含 total, used 和 free 属性，分别表示总计、已使用和未使用空间的字节数。 path 可以是一个文件或是一个目录。



#### shutil.**chown**(*path*, *user=None*, *group=None*)

修改给定 *path* 的所有者 *user* 和/或 *group*。

*user* 可以是一个系统用户名或 uid；*group* 同样如此。 要求至少有一个参数。



#### shutil.**which**(*cmd*, *mode=os.F_OK | os.X_OK*, *path=None*)

返回当给定的 *cmd* 被调用时将要运行的可执行文件的路径。 如果没有 *cmd* 会被调用则返回 `None`。

*mode* 是一个传递给 [`os.access()`](https://docs.python.org/zh-cn/3/library/os.html#os.access) 的权限掩码，在默认情况下将确定文件是否存在并且为可执行文件。

当未指定 *path* 时，将会使用 [`os.environ()`](https://docs.python.org/zh-cn/3/library/os.html#os.environ) 的结果，返回 "PATH" 值或回退为 [`os.defpath`](https://docs.python.org/zh-cn/3/library/os.html#os.defpath)。
