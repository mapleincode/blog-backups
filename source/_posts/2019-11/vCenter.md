---
date: 2019-11-11 02:31:16
title: vCenter 和 vCSA 对比
tags: [ vmware ]
---

vCenter Server与vCenter Server Virtual Appliance（vCSA）都提供对企业中[VMware vSphere](https://baike.baidu.com/item/VMware vSphere)部署的集中管理。虽然拥有相同的功能，两者安装却不同，所支持的功能也不同。

**vCSA不支持的功能**

vCSA安装在用于VMware的SUSE Linux Enterprise Server上，前提是拥有vSphere许可。VCSA 5.0.1及以后版本运行[PostgreSQL](https://baike.baidu.com/item/PostgreSQL)数据库，而之前版本运行的是IBM DB2数据库。VCSA只支持PostgreSQL数据库，所以如果Microsoft SQL Server或者IBM DB2更能轻松集成在环境中的话，vCSA不是最佳选择。

vCSA也不支持链接模式Linked Mode，该功能能让多个vCenter Server实例链接在一起以管理大型环境。一个vCenter Server实例能管理高达300到3000台的虚拟机。在链接模式下，可以跨十个vCenter Server实例管理1000到上万台[虚拟机](https://baike.baidu.com/item/虚拟机)。这种设置也可让你为管理员角色创建全局定义，并能跨多个vCenter Server实例搜寻库存资源。

此外，vCSA不支持IPv6，虽然该功能没有Linked Mode应用得广泛。甚至就算设备运行在默认支持IPv6的操作系统SUSE Linux上，仍不支持IPv6。

**vCenter Server和vCSA安装对比**

在知道vCSA所不能支持的功能后，用户可能倾向于安装完整版的vCenter Server。然而，安装vCSA比完整版更容易。

首先在计算机上下载.VMDK与.OVF文件。然后在vSphere [Client](https://baike.baidu.com/item/Client)的文件菜单上通过选择Deploy OVF Template部署vCSA。

vCenter Server完整版确实支持更多数据库、Linked Mode与[IPv6](https://baike.baidu.com/item/IPv6)，但安装过程要求较多。首先需要64位[Windows Server 2008 R2](https://baike.baidu.com/item/Windows Server 2008 R2)版本，受支持的数据库，如Microsoft SQL Server Express。还有其他要求，如Microsoft .NET 3.5 SP1架构或Microsoft Windows Installer 4.5。最后，还需要一台安装vCenter Server的物理或虚拟服务器。

通常，[数据中心](https://baike.baidu.com/item/数据中心)的大小决定着是选择vCenter Server还是vCSA。 [1]



> 尝试安装了 vCSA。其实类似 vCenter 更注重的是对集群的管理，但对于我这种个人而言，vCenter 的意义不是特别大，即使功能更加丰富，你也得额外开销一个虚拟机用于运行 vCenter 服务。而 ESXi 自带的功能已经足够满足一个个人的使用了。