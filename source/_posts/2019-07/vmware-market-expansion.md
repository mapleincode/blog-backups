---
title: VMware 虚拟机硬盘文件扩容
date: 2018-11-01 11:25:38
tags: [ vmware ]
---

因为开发和测试需要，在家里常年跑了 vmware 虚拟机。然后通过 MAC 端的 VMware Fusion 对其进行连接。在上面操作。



前段时间 win10 推送了一个大版本更新，需要升级大版本就要把老的数据备份，然而我之前分配的硬盘空间不足以进行升级。而我实在不想把开虚拟机的电脑接到显示器上，尤其是我知道那张显卡已经基本无法显示了。



我想到了 VMware 一向都有完善的命令行工具，是否可以通过命令行工具来实现对硬盘文件的扩容。查阅了网上资料，卧槽，还真的可以的。



```bash
maple@maple:~$ vmware-vdiskmanager
VMware Virtual Disk Manager - build 4638234.
Usage: vmware-vdiskmanager OPTIONS <disk-name> | <mount-point>
Offline disk manipulation utility
  Operations, only one may be specified at a time:
     -c                   : create disk.  Additional creation options must
                            be specified.  Only local virtual disks can be
                            created.
     -d                   : defragment the specified virtual disk. Only
                            local virtual disks may be defragmented.
     -k                   : shrink the specified virtual disk. Only local
                            virtual disks may be shrunk.
     -n <source-disk>     : rename the specified virtual disk; need to
                            specify destination disk-name. Only local virtual
                            disks may be renamed.
     -p                   : prepare the mounted virtual disk specified by
                            the mount point for shrinking.
     -r <source-disk>     : convert the specified disk; need to specify
                            destination disk-type.  For local destination disks
                            the disk type must be specified.
     -x <new-capacity>    : expand the disk to the specified capacity. Only
                            local virtual disks may be expanded.
     -R                   : check a sparse virtual disk for consistency and attempt
                            to repair any errors.
     -e                   : check for disk chain consistency.
     -D                   : make disk deletable.  This should only be used on disks
                            that have been copied from another product.

  Other Options:
     -q                   : do not log messages

  Additional options for create and convert:
     -a <adapter>         : (for use with -c only) adapter type
                            (ide, buslogic, lsilogic). Pass lsilogic for other adapter types.
     -s <size>            : capacity of the virtual disk
     -t <disk-type>       : disk type id

  Disk types:
      0                   : single growable virtual disk
      1                   : growable virtual disk split in 2GB files
      2                   : preallocated virtual disk
      3                   : preallocated virtual disk split in 2GB files
      4                   : preallocated ESX-type virtual disk
      5                   : compressed disk optimized for streaming
      6                   : thin provisioned virtual disk - ESX 3.x and above

     The capacity can be specified in sectors, KB, MB or GB.
     The acceptable ranges:
                           ide/scsi adapter : [1MB, 8192.0GB]
                           buslogic adapter : [1MB, 2040.0GB]
        ex 1: vmware-vdiskmanager -c -s 850MB -a ide -t 0 myIdeDisk.vmdk
        ex 2: vmware-vdiskmanager -d myDisk.vmdk
        ex 3: vmware-vdiskmanager -r sourceDisk.vmdk -t 0 destinationDisk.vmdk
        ex 4: vmware-vdiskmanager -x 36GB myDisk.vmdk
        ex 5: vmware-vdiskmanager -n sourceName.vmdk destinationName.vmdk
        ex 6: vmware-vdiskmanager -r sourceDisk.vmdk -t 4 -h esx-name.mycompany.com \
              -u username -f passwordfile "[storage1]/path/to/targetDisk.vmdk"
        ex 7: vmware-vdiskmanager -k myDisk.vmdk
        ex 8: vmware-vdiskmanager -p <mount-point>
              (A virtual disk first needs to be mounted at <mount-point>)
```

VMware 还很贴心的给了 example。

按照上面的提示，我们需要的是:

```bash
vmware-vdiskmanager -x 36GB myDisk.vmdk
```

关闭虚拟机，然后输入命令指令，大约花了几分钟就扩容完毕了。

之后会给出一个 warn，意思是说虽然硬盘扩容了，但是系统本身的磁盘容量还是不变的，需要第三方工具对系统分区进行扩容。

然后进入系统，对硬盘进行扩容，就能成功升级了。

