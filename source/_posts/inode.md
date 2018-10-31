---
title: 优化 Ext4 分区 inode 占用空间问题
date: 2017-11-20 07:11:49
tags: [ Linux ]
---

>   对乱七八糟的知识点整理，方便下次使用



>   去年年底购入了渣雷的下载宝，用  ntfs 格式化后，性能一直不佳(后面发现是因为那个渣系统会索引文件)，后用 ext4 格式，但是格式之后发现居然少了 15G 左右。



## inode

**inode**是指在许多“类[Unix](https://zh.wikipedia.org/wiki/Unix)[文件系统](https://zh.wikipedia.org/wiki/%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F)”中的一种[数据结构](https://zh.wikipedia.org/wiki/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84)。每个inode保存了文件系统中的一个**文件系统对象**（包括[文件](https://zh.wikipedia.org/wiki/%E8%AE%A1%E7%AE%97%E6%9C%BA%E6%96%87%E4%BB%B6)、[目录](https://zh.wikipedia.org/wiki/%E7%9B%AE%E5%BD%95_(%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F))、[设备文件](https://zh.wikipedia.org/wiki/%E8%AE%BE%E5%A4%87%E6%96%87%E4%BB%B6)、[socket](https://zh.wikipedia.org/w/index.php?title=%E8%BF%9B%E7%A8%8B%E9%97%B4%E9%80%9A%E4%BF%A1socket&action=edit&redlink=1)、[管道](https://zh.wikipedia.org/wiki/%E7%AE%A1%E9%81%93_(Unix)), 等等）的元信息数据，但不包括数据内容或者文件名。(from: [维基百科](https://zh.wikipedia.org/wiki/Inode))



通俗的说，inode 其实就是文件的索引值。



inode 也会消耗硬盘空间，所以硬盘格式化的时候，操作系统自动将硬盘分成两个区域。一个是数据区，存放文件数据；另一个是 inode 区（inode table），存放 inode 所包含的信息。

每个 inode 节点的大小，一般是 128 字节或 256 字节。inode 节点的总数，在格式化时就给定，一般是每1KB或每2KB 就设置一个 inode。假定在一块 1GB 的硬盘中，每个 inode 节点的大小为 128 字节，每 1KB 就设置一个inode，那么 inode table 的大小就会达到 128MB，占整块硬盘的 12.8%。



一个 inode 大小现在默认值是 256 字节，默认是 16K 一个 inode。也就是默认 inode 会消耗 1/64 硬盘容量也就是 16GB。



正常系统装机，这样分配 inode 数量并没有什么问题，因为系统安装会有大量的小文件。但是移动硬盘、存储盘，往往存放的是大体积的小姐姐(大雾)，所以不需要太多的 inode。



## 格式化硬盘

默认格式化:

```bash
sudo mkfs.ext4 -n /dev/sdc1 // -n 表示模拟，如果不加就真的执行了
```

`mkfs.ext4` 有两个参数:

```
-i bytes-per-inode // 多少个字节一个 inode
-N number-of-inodes // 固定 inode 数量
```



默认 16k 相当于:

```bash
sudo mkfs.ext4 -i 16384 -n /dev/sdc
```

只要修改 `-i` 参数，就可以达到减少 inode 数量的目的。(比如 256 k 相当于 16384 * 16 = 262144)。



除了自定义选择参数，还有一些默认参数配置在`/etc/mke2fs.conf `。

```sh
small = {
    blocksize = 1024
    inode_size = 128
    inode_ratio = 4096
}
floppy = {
    blocksize = 1024
    inode_size = 128
    inode_ratio = 8192
}
big = {
    inode_ratio = 32768
}
huge = {
    inode_ratio = 65536
}
news = {
    inode_ratio = 4096
}
largefile = {
    inode_ratio = 1048576
    blocksize = -1
}
largefile4 = {
    inode_ratio = 4194304
    blocksize = -1
}
hurd = {
     blocksize = 4096
     inode_size = 128
}
// blocksize 文件块 默认 4k
// inode_ratio inode 比率? 表示若干字节需要一个 inode
// inode_size inode 大小，默认 256 字节

```

使用方式:

```sh
sudo mkfs.ext4 -T largefile -n /dev/sdc
```



参照:

1.  [优化 Ext4 分区格式化后占用空间(通过降低inode数来节省磁盘空间)](http://blog.csdn.net/hunanchenxingyu/article/details/41832639)
2.  [理解inode](http://www.ruanyifeng.com/blog/2011/12/inode.html)
