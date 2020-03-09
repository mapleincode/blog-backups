---
title: 使用 Unixbench 测试 CPU 性能
date: 2018-03-18 03:36:52
tags: [ CPU, Linux ]
---

Unixbench 是 Linux 用来测试 CPU 综合性能的工具。

```sh
#! /bin/bash
#==============================================================#
#   Description:  Unixbench script                             #
#   Author: Teddysun <i@teddysun.com>                          #
#   Intro:  https://teddysun.com/245.html                      #
#==============================================================#

# Check System
[[ $EUID -ne 0 ]] && echo 'Error: This script must be run as root!' && exit 1
[[ -f /etc/redhat-release ]] && os='centos'
[[ ! -z "`egrep -i debian /etc/issue`" ]] && os='debian'
[[ ! -z "`egrep -i ubuntu /etc/issue`" ]] && os='ubuntu'
[[ "$os" == '' ]] && echo 'Error: Your system is not supported to run it!' && exit 1

# Install necessary libaries
if [ "$os" == 'centos' ]; then
    yum -y install make automake gcc autoconf gcc-c++ time perl-Time-HiRes
else
    apt-get -y update
    apt-get -y install make automake gcc autoconf time perl
fi

# Create new soft download dir
mkdir -p /opt/unixbench
cd /opt/unixbench
cur_dir=`pwd`

# Download UnixBench5.1.3
if [ -s UnixBench5.1.3.tgz ]; then
    echo "UnixBench5.1.3.tgz [found]"
else
    echo "UnixBench5.1.3.tgz not found!!!download now..."
    if ! wget -c https://blog.mpae.cc/UnixBench5.1.3.tgz; then
        echo "Failed to download UnixBench5.1.3.tgz, please download it to ${cur_dir} directory manually and try again."
        exit 1
    fi
fi
tar -xzf UnixBench5.1.3.tgz
cd UnixBench/

#Run unixbench
make
./Run

echo ''
echo ''
echo "======= Script description and score comparison completed! ======= "
echo ''
echo ''

```

找到一位大佬写的脚本。包括下载安装包，依赖并测试。

## 运行方式

```sh
wget http://blog.mpae.cc/unixbench.sh # 感谢 GayHub
chmod +x unixbench.sh
(sudo) ./unixbench.sh
```

## Result

这是我测试一台 VPS 的结果


```sh
   #    #  #    #  #  #    #          #####   ######  #    #   ####   #    #
   #    #  ##   #  #   #  #           #    #  #       ##   #  #    #  #    #
   #    #  # #  #  #    ##            #####   #####   # #  #  #       ######
   #    #  #  # #  #    ##            #    #  #       #  # #  #       #    #
   #    #  #   ##  #   #  #           #    #  #       #   ##  #    #  #    #
    ####   #    #  #  #    #          #####   ######  #    #   ####   #    #

   Version 5.1.3                      Based on the Byte Magazine Unix Benchmark

   Multi-CPU version                  Version 5 revisions by Ian Smith,
                                      Sunnyvale, CA, USA
   January 13, 2011                   johantheghost at yahoo period com


1 x Dhrystone 2 using register variables  1 2 3 4 5 6 7 8 9 10

1 x Double-Precision Whetstone  1 2 3 4 5 6 7 8 9 10

1 x Execl Throughput  1 2 3

1 x File Copy 1024 bufsize 2000 maxblocks  1 2 3

1 x File Copy 256 bufsize 500 maxblocks  1 2 3

1 x File Copy 4096 bufsize 8000 maxblocks  1 2 3

1 x Pipe Throughput  1 2 3 4 5 6 7 8 9 10

1 x Pipe-based Context Switching  1 2 3 4 5 6 7 8 9 10

1 x Process Creation  1 2 3

1 x System Call Overhead  1 2 3 4 5 6 7 8 9 10

1 x Shell Scripts (1 concurrent)  1 2 3

1 x Shell Scripts (8 concurrent)  1 2 3

========================================================================
   BYTE UNIX Benchmarks (Version 5.1.3)

   System: 10-254-92-97: GNU/Linux
   OS: GNU/Linux -- 4.4.0-116-generic -- #140-Ubuntu SMP Mon Feb 12 21:23:04 UTC 2018
   Machine: x86_64 (x86_64)
   Language: en_US.utf8 (charmap="ANSI_X3.4-1968", collate="ANSI_X3.4-1968")
   CPU 0: Intel(R) Xeon(R) CPU E5-2650 v4 @ 2.20GHz (4389.8 bogomips)
          x86-64, MMX, Physical Address Ext, SYSENTER/SYSEXIT, SYSCALL/SYSRET
   03:41:06 up  5:46,  2 users,  load average: 0.08, 0.02, 0.01; runlevel 5

------------------------------------------------------------------------
Benchmark Run: Sun Mar 18 2018 03:41:06 - 04:08:56
1 CPU in system; running 1 parallel copy of tests

Dhrystone 2 using register variables       28086166.5 lps   (10.0 s, 7 samples)
Double-Precision Whetstone                     2267.4 MWIPS (10.0 s, 7 samples)
Execl Throughput                               4425.3 lps   (30.0 s, 2 samples)
File Copy 1024 bufsize 2000 maxblocks        665078.5 KBps  (30.0 s, 2 samples)
File Copy 256 bufsize 500 maxblocks          173748.5 KBps  (30.0 s, 2 samples)
File Copy 4096 bufsize 8000 maxblocks       2164319.8 KBps  (30.0 s, 2 samples)
Pipe Throughput                              895617.7 lps   (10.0 s, 7 samples)
Pipe-based Context Switching                 166209.4 lps   (10.0 s, 7 samples)
Process Creation                              12421.8 lps   (30.0 s, 2 samples)
Shell Scripts (1 concurrent)                   8285.9 lpm   (60.0 s, 2 samples)
Shell Scripts (8 concurrent)                   1098.0 lpm   (60.0 s, 2 samples)
System Call Overhead                         862020.4 lps   (10.0 s, 7 samples)

System Benchmarks Index Values               BASELINE       RESULT    INDEX
Dhrystone 2 using register variables         116700.0   28086166.5   2406.7
Double-Precision Whetstone                       55.0       2267.4    412.3
Execl Throughput                                 43.0       4425.3   1029.1
File Copy 1024 bufsize 2000 maxblocks          3960.0     665078.5   1679.5
File Copy 256 bufsize 500 maxblocks            1655.0     173748.5   1049.8
File Copy 4096 bufsize 8000 maxblocks          5800.0    2164319.8   3731.6
Pipe Throughput                               12440.0     895617.7    719.9
Pipe-based Context Switching                   4000.0     166209.4    415.5
Process Creation                                126.0      12421.8    985.9
Shell Scripts (1 concurrent)                     42.4       8285.9   1954.2
Shell Scripts (8 concurrent)                      6.0       1098.0   1830.1
System Call Overhead                          15000.0     862020.4    574.7
                                                                   ========
System Benchmarks Index Score                                        1124.1



======= Script description and score comparison completed! =======

```

## Clean

文件路径为: `/opt/unixbench`。直接删除该文件夹即可。

