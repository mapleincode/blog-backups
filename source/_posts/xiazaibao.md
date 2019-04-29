---
title: 迅雷下载宝定制固件
date: 2019-04-29 18:26:53
---

转载 <https://www.right.com.cn/forum/thread-186873-1-1.html>。

此文是为了备份。



迅雷下载宝官方固件因追求稳定而不开放ssh和telnet。为了进一步挖潜下载宝性能，在

保留官

方百分之百功能

的基础上，定制了下载宝固件，仅供广大高清爱好者学习研究，

若有违官方政策，请自觉删除！

 

为了稳定起见，本固件只基于官方手机APP正式推送的版本进行定制，例如191、192版。

 

【2017年10月12日复活版】

 

鉴于下载宝官方开始更新固件，且各位网友极力更新定制固件，本人决定继续更新维护下载宝的定制固件版本，供大伙研究使用。

 

因本人下载宝已出，在此要特别感谢@tonymoses朋友提供远程调试环境，并极其耐心地协助做好测试工作！

 

复活版之后的持续更新版本原则上只是更新官方基础固件，定制功能不再做扩展，当然有些必要的更新视情况会做的。

 

 

171012复活版

 

1、基于官方338固件进行扩展和定制。

 

2、功能与170508终极版相同。

 

3、因有人反映Transmission的web界面有bug，故更新到最新的

WEB Control: 1.1 Beta(20170606)。

 

注：再次强调从318版开始，luci访问已恢复默认80端口。

 

 

【2017年5月8日终极版】

 

鉴于下载宝官方论坛自2017年2月9日之后再无任何官方信息和对于用户发帖的任何回应，另外本人迅雷会员到期后已不再续费，因此，2017年5月8日之后本人不再基于官方固件做更新。

 

期间本人试用过自行编译的LEDE固件和pandorabox固件。LEDE是纯开源系统，使用的是最新的linux内核和最新的package，有着更好的扩展性和灵活性。但其使用的是MTK的开源驱动，IO性能较弱。而pandorabox虽然不开源，内核老旧，但其使用的是MTK闭源驱动，对NTFS硬盘支持也很好，经实测写入速度能达到70MB/s，读取达到千兆网满速。

 

因此，综合评估下来，本人目前日常使用的是pandorabox。之后是否会继续推出基于pandorabox的下载宝定制固件，要视情况而定。

 

 

170508终极版：

 

 

1、全面基于官方327版固件进行扩展和定制，luci访问地址 http://下载宝ip，默认用户名：root，密码：admin

 

2、采用全新架构扩展SD卡

，可作为ROM直接安装组件。ROM的容量可扩展到下载宝所能识别的SD卡容量。

 

3、可挂载远程

NFS服务器和Samba服务器的硬盘为本地路径，不再受移动硬盘的容量限制。

 

​    远程挂载NFS服务器：mount -t nfs -o iocharset=utf8

,

nolock,rw 远程ip:/输出路径 /本地挂载点

 

​    远程挂载Samba服务器：mount -t cifs -o iocharset=utf8,username=xxx,password=xxx //远程ip/共享路径 /本地挂载点

 

​    若本地挂载点设为：/data/UsbDisk1/VolumeX/TDDOWNLOAD，则下载宝的下载点即为远程服务器。其中VolumeX要根据系统挂载点而定。

 

4、定制功能包括：NFS服务器、FTP服务器、Transmission、Aria2、Ngrok，且均可在luci界面进行配置。若需要其他功能请自行通过luci或opkg进行安装，安装方法与openwrt相同。

 

5、nfs server默认启动。其他功能默认关闭，须在luci的服务菜单里手动打开需要的功能。一旦启用，下次开机则会自动运行。若不需要nfs server，停止命令：mynfs stop，启动命令：mynfs start

 

6、提供smartmontools，可查看外接硬盘的smart信息包括硬盘温度等。查看命令为：smt

 

7、可暂停下载宝原生服务，命令为：myxzb stop，启动命令为：myxzb start

 

8、Transmission：
    1）主程序为最新2.92版。
    2）Web Control UI更新为20170317版。
    3）提供了3个Transmission的chrome插件（可任选其一）供下载使用，基于chrome内核的浏览器同样适用，可右键直接添加下载任务。
    4）提供了最强大的android APP供下载使用。
    5）关于Transmission的建议：
       a、在计划任务里写入守护命令：*/5 * * * * /usr/ubin/mymon，在Transmission异常退出后会自动重启。其中5代表5分钟，可自行修改。
       b、最好暂停下载宝服务，因其负载太高。在计划任务里写入守护命令：*/5 * * * * /usr/ubin/mymonall ，mymonall已包含a中的监控。
       c、缓存设置不超过32M，16M为宜，否则容易异常退出。
       d、在SD卡里创建一个不小于64M的swap分区，在luci的挂载点里有swap分区的挂载选项。

 

9、Aria2：
    1）主程序为最新1.31.0版，支持BT和磁力下载，支持断点续传。
    2）luci新增token方式的RPC认证，替换掉用户密码方式。RPC设置为：  http://token:xxx@下载宝ip:6800/jsonrpc，其中xxx是luci界面里设置的token值。
    3）luci新增“证书检验”选项，若用于百度云离线下载，此选项必须为空，否则无法正常下载。
    4）luci新增“log记录等级”选项，默认为“notice"，解决了log文件体积增长迅速的问题。
    5）web管理采用YAAW，访问aria2c.com，即可进行下载管理，页面已汉化。
    6）新增Aria-Ng Web UI，功能比YAAW更为强大。访问地址：  http://下载宝ip/aria2
10、智能识别SD卡，若SD卡装有固件则自动扩展，否则作为普通SD卡使用。在不使用SD卡扩展固件时，本固件功能与官方一致。

 

**安装方法：**

1、确保Bootloader为

官方uboot

，若已经刷了breed则必须

刷回官方uboot。

方法详见下方

【固件升级】。

 

2、升级327.170326版固件，方法详见底部

【固件升级】

。

 

3、下载rom.tar固件包，

直接拷贝

到SD卡根目录下。SD卡支持

任意

文件系统格式，

无需格式化

。

 

4、将SD卡插入下载宝。

 

5、重启下载宝，等led白灯常亮后会再次重启，此时ROM已扩展为sd卡容量。

若SD卡下载宝不好识别，则多断电插拔重启几次。实在不行只能另行换卡。

 

6、若只需要官方功能，则不必安装SD卡。

 

 

【2017年3月30日更新】

 

修正了327版无法挂载cifs的问题。

 

 

【2017年3月29日更新】

 

1、更新了Aria2的配置，确保断点续传。

 

​     刷机后需要在YAAW配置界面内填写自己的ip：    http://ip:6800/jsonrpc

 

​     若在luci中开启了RPC认证，则YAAW设置为:     http://user:passwd@ip:6800/jsonrpc

 

2、更新了Transmission的配置，一般情况下刷机后选择启用即可。

 

3、修正了NFS Server 配置无法生效的问题（318版无此问题）。

 

 

【2017年3月28日更新】

 

1、修正了327版固件ip分配的问题。

 

2、更新了Aria2，采用自行编译的版本，支持BT和磁力链接。

 

3、重新制作了318版全功能版。

 

 

【2017年3月26日更新】

 

1、更新官方327版固件。

 

2、新增Aria2，web管理采用YAAW，访问aria2c.com，即可进行下载管理，页面已汉化。

 

3、新增FTP服务器。

 

4、新增smartmontools，可查看外接硬盘的smart信息。查看命令为：smt

 

5、可暂停下载宝原生服务，命令为：myxzb stop，启动命令为：myxzb start

 

6、nfs server默认启动。其他功能默认关闭，须在luci的服务菜单里手动打开需要的功能。一旦启用，下次开机则会自动运行。若不需要nfs server，停止命令：mynfs stop，启动命令：mynfs start

 

7、关于Transmission的建议：

 

​    1）在计划任务里写入守护命令：*/5 * * * * /usr/ubin/mymon，在Transmission异常退出后会自动重启。其中5代表5分钟，可自行修改。

 

​    2）最好暂停下载宝服务，因其负载太高。在计划任务里写入守护命令：*/5 * * * * /usr/ubin/mymonall ，mymonall已包含1）的监控。

 

​    3）缓存设置不超过32M，否则容易异常退出。

 

​    4）在sd卡里创建一个不小于64M的swap分区，在luci的挂载点里有swap分区的挂载选项。

 

8、其他功能及安装方法详见1月8日更新说明。

 

 

【2017年1月8日更新】-- 里程碑重大更新

 

1、全面基于318版固件进行扩展和定制；

 

2、采用全新架构

扩展SD卡

，可作为ROM直接安装组件。ROM的容量可扩展到下载宝所能识别的SD卡容量。

 

3、可挂载

远程

NFS服务器和Samba服务器的硬盘为本地路径，不再受移动硬盘的容量限制。

 

​    远程挂载NFS服务器：mount -t nfs -o nolock,rw 远程ip:/输出路径 /本地挂载点

 

​    远程挂载Samba服务器：mount -t cifs -o username=xxx,password=xxx //远程ip/共享路径 /本地挂载点

 

​    若本地挂载点设为：/data/UsbDisk1/VolumeX/TDDOWNLOAD，则下载宝的下载点即为远程服务器。其中VolumeX要根据系统挂载点而定。

 

 

4、定制功能只提供NFS服务器、Transmission和Ngrok，且均可在luci界面进行配置，因此取消自启动设置功能。若需要其他功能请自行通过luci或opkg进行安装，安装方法与openwrt相同。

 

5、

智能识别

SD卡，若SD卡装有固件则自动扩展，否则作为普通SD卡使用。在不使用SD卡扩展固件时，本固件功能与官方一致。

 

 

**安装方法：**

1、确保Bootloader为

官方uboot

，若已经刷了breed则必须

刷回官方uboot。

方法详见下方

【固件升级】。

 

2、升级318.170108版固件，方法详见下方

【固件升级】

。

 

3、下载rom.tar固件包，

直接拷贝

到SD卡根目录下。SD卡支持

任意

文件系统格式，

无需格式化

。有的SD卡下载宝不好识别的话，最好格式化成NTFS。

 

4、将SD卡插入下载宝。

 

5、重启下载宝，等led白灯常亮后即可正常使用，此时ROM已扩展为sd卡容量。

 

6、若只需要官方功能，则不必安装SD卡。

 

7、因官方视频点播不再占用80端口，luci改回常规的80端口。

 

8、luci及其他登录用户名：root，密码：admin

 

 

【2017年1月1日】

 

1、官方已推出318版固件，此版固件是官方基于3.10.14内核的Openwrt自行编译的，脱离原先的Pandorabox架构，极大程度上提高了对底层的控制。也就是说，从318版之后，下载宝固件已脱胎换骨。

 

2、官方针对第三方Bootloader做了限制，也就是说刷了breed的下载宝升级318版之后无法获取ip地址。同时，也对其他方面做了权限限制。

 

3、因此，本学习固件的定制功能不再跟随官方固件升级和维护，285.160831是全功能的终结版。

 

4、为了能顺利升级318版固件，特提供官方uboot的下载，直接在breed里"更新固件/Bootloader"选项下刷入即可。要注意的是，

官方uboot下刷机是“捅菊花”，而不是按“键盘弹出”键。

 

5、为了便于部分爱好者进一步研究学习下载宝固件，以后会继续提供root版固件。仅作学习研究之用，

若有违官方政策，请自觉删除！

 

 

**【8月31日更新】**

 

1、基于官方固件285版。

2、加入Aria2 1.25.0，更新自动启动选项。

 

3、Transmission升级为2.92-3。

4、分为两个版本，无后缀的是全功能版本，luciroot后缀的是带luci界面、开放ssh的root版。

 

5、优化opt安装方式，直接拷贝，不必解压。

 

 

**安装方法：**
1、升级285.160831版固件，方法详见下方【固件升级】。
2、下载opt.tgz运行包，直接拷贝到SD卡上，根目录下绝对不能有opt目录。SD卡必须是NTFS格式。
3、将SD卡插入下载宝。
4、重启下载宝，即可正常使用。
5、若不需要Transmission、百度云同步、Aria2和Ngrok功能，则不必安装SD卡。

 

6、关于Aria2，

需要注意以下几点：

 

​    1）登陆aria2c.com（指向本地web），设置里的RPC Path要改为：

http://token: Passw0rd@下载宝ip:6800/jsonrpc （删除：与P之间的空格）。

 

​    2）若要使用迅雷离线和百度网盘的下载资源，还需要安装浏览器插件。

浏览器要用chrome原版，其他基于chrome内核的改版浏览器都不一定有效。

 

7、官方未标明285版是否稳定，因此保留192稳定版的下载链接。

 

 

**【8月28日更新】**

 

1、官方固件同步升级为285版（看不懂的版本号）。

2、opt应用包不更新。

 

3、分为两个版本，无后缀的是全功能版本，luciroot后缀的是带luci界面、开放ssh的root版。

 

4、rom空间已所剩无几，为稳定起见，其他功能统统不再增加。

 

5、关于Aria2，若需要的朋友可以自行安装。安装脚本为：ipkg upate && ipkg upgrade && ipkg install aria2，具体配置和使用方法详见官方文档。

需要注意以下几点：

 

​    1）登陆aria2c.com（指向本地web），设置里的RPC Path要改为：

http://token: Passw0rd@下载宝ip:6800/jsonrpc （删除：与P之间的空格）。

 

​    2）若要使用迅雷离线和百度网盘的下载资源，还需要安装浏览器插件。

浏览器要用chrome原版，其他基于chrome内核的改版浏览器都不一定有效。

 

​    3）ipkg upgrade命令会升级transmission到2.92-3版，因此需要将

transmission-control-full.tar解包全部覆盖 

/opt/share/transmission/web目录。

 

6、官方未标明285版是否稳定，因此保留192稳定版的下载链接。

 

 

**安装方法：**
1、升级285.160828版固件，方法详见下方【固件升级】。
2、下载160608版opt运行包，将“opt”目录解压到SD卡上，即在SD卡根目录下建有“opt”文件夹。SD卡最好是NTFS格式。
3、将SD卡插入下载宝。
4、重启下载宝，即可正常使用。
5、若不需要Transmission、百度云同步和Ngrok功能，则不必安装SD卡。

 

**【6月10日更新】**

 

1、官方固件同步升级为192稳定版，opt应用包不更新。

 

2、加入FTP服务器，并整合入自启动选项。vsftpd已升级到最新3.0.3版本，解决了可能存在的不兼容问题。

 

![img](/Users/maple/code-try/blog-backups/source/_posts/assets/181123ebbaxntnwn06znaa-20190429182804253.png) 

 

 

**安装方法：**
1、升级160610版固件，方法详见下方【固件升级】。
2、下载160608版opt运行包，将“opt”目录解压到SD卡上，即在SD卡根目录下建有“opt”文件夹。SD卡最好是NTFS格式。
3、将SD卡插入下载宝。
4、重启下载宝，即可正常使用。
5、若不需要Transmission、百度云同步和Ngrok功能，则不必安装SD卡。

 

**FTP配置方法：**
1、登陆web管理界面：http://下载宝ip:88，默认用户名root，密码admin
2、进入“服务/FTP服务器”。

 

3、选项卡“全局”下的“启用”项改动后“保存&应用”就会实时启动和停止FTP服务。若在开机自启动中勾选“FTP服务器”，则不论是否选中“启动”，都会开机自启动。

 

![img](/Users/maple/code-try/blog-backups/source/_posts/assets/181124jld0r4yyqltsrwkh-20190429182804254.png) 

 

 

4、默认允许本地用户访问（用户名root，密码admin）。本地用户的根目录在“编辑模板”选项卡中设置，默认为：/data

 

![img](/Users/maple/code-try/blog-backups/source/_posts/assets/181124p03notnhqnnh3hib-20190429182804255.png) 

 

 

5、默认禁止匿名用户，若需要请自行启用。匿名用户的根目录在“匿名用户”选项卡中设置，默认为：/data

 

![img](/Users/maple/code-try/blog-backups/source/_posts/assets/181125lj0zzqalag4gelf9-20190429182804256.png) 

 

 

**【6月8日更新】**

 

1、优化改进Samba共享

 

  a）用“UsbDisk”代替共享中文名“移动磁盘"；

 

  b）共享SD卡；

 

  c）开启用户身份验证，在“开机自启动”选项中勾选“启用Samba验证”，输入Samba密码，用户名默认为root。

 

  d）预留了用户自定义接口，只要将自定义smb.conf文件拷入SD卡的/opt/etc文件夹即可。

若不需要自定义，请务必删除此文件。

 

  e）以上功能在重启下载宝或热插拔硬盘后生效。

 

![img](/Users/maple/code-try/blog-backups/source/_posts/assets/181126tmaryby88v2orb4v-20190429182804257.png) 

 

 

2、Transmission加入了用户身份验证，默认用户名：root，密码：admin

 

3、将“禁止硬盘休眠”功能改为：保留硬盘休眠的基础上，禁止硬盘磁头复位。

 

 

**【6月1日更新】-里程碑版本**

 

1、加入开机自启动选项功能，用户可以根据自己的需求定制开启哪些功能，降低下载宝负载。设置完成后点击“保存&应用”，重启下载宝生效。百度云同步、Transmission、Ngrok内网穿透需要安装SD卡。

 

![img](/Users/maple/code-try/blog-backups/source/_posts/assets/181123hx3b6bcq9q88xna6-20190429182804257.png) 

 

 

2、取消hdp版本固件，禁止硬盘休眠功能整合进“开机自启动”选项。

 

3、百度云同步SyncY设置页面取消了“开机自动重启”选项，统一整合进“开机自启动”页面。

 

 

Luci图形管理界面：http://下载宝ip:88，默认用户名：root，密码：admin

 

 

**【5月30日更新】**

 

加入Ngrok最新1.37版（全新编译MT7621专用版），完全luci界面配置管理，绝对的内网穿透神器！可穿透N层内网，无须在路由器上做任何设置。实在是小运营商网络用户的福音！Transmission和百度云同步的远程管理不再只是梦想！

 

![img](/Users/maple/code-try/blog-backups/source/_posts/assets/075719nsh61xxpbx1nozxo-20190429182804258.png) 

 

 

**安装方法：**1、升级160530版固件，方法详见下方**【固件升级】**。
2、下载160530版opt运行包，将“opt”目录解压到SD卡上，即在SD卡根目录下建有“opt”文件夹。SD卡最好是NTFS格式。
3、将SD卡插入下载宝。
4、重启下载宝，即可正常使用。
5、若不需要Transmission、百度云同步和Ngrok功能，则不必安装SD卡。

 

**Ngrok配置方法：**

 

1、为拥有固定的二级域名，请事先注册Ngrok账号。推荐：

www.ngrok.cc/login

 

2、登陆web管理界面：http://下载宝ip:

88

，默认

用户名root，密码admin

 

3、进入“服务/Ngrok配置”，首先配置服务器，其中“令牌”是在注册用户时获取的。

 

![img](/Users/maple/code-try/blog-backups/source/_posts/assets/080923w0g80guw0zg5ug94-20190429182804259.png) 

 

4、其次设置通道。Ngrok支持http、https、TCP端口三种方式，其中：子域名和远程端口都是在Ngrok网站上设置开通。本地地址要用真实ip，

不能填“127.0.0.1”

。概览界面中的http(s)的URL有误，访问域名应为：

子域名.ngrok.cc。

 

![img](/Users/maple/code-try/blog-backups/source/_posts/assets/075738rwytoi1g410lyzz0-20190429182804260.png) 

 

![img](/Users/maple/code-try/blog-backups/source/_posts/assets/075743v0ffxf49sl9l0d0h-20190429182804261.png) 

 

5、勾选通道的“启用”选项，点击“保存&应用”之后，即开始在后台运行Ngrok。清空“启用”选项并“保存&应用”，则停止Ngrok服务。

 

6、若要开机自启动，请登陆ssh用命令行开启：/etc/init.d/ngrokc enable

 

 

**【5月29日更新】**
1、加入luci图形管理界面

 

a) 因下载宝视频播放占用了80端口，luci改用88端口，请访问：http://下载宝ip:88，web登陆默认用户名root，密码admin。

 

b) 因下载宝的闪存只有16M，空间已所剩无几，在此界面上基本无法再安装合适的应用包。不过，可以通过命令行：opkg install -d sd XXXX 将应用安装到SD卡上。
![img](/Users/maple/code-try/blog-backups/source/_posts/assets/070403rj3djq1j1fj9jfpw-20190429182804262.png) 

 

2、加入百度云同步SyncY。已完美实现web页面的“绑定”“开机自动启动”"启动”“停止”等功能，感谢坛友lm789632的提示。
3、上一版固件中，Transmission在更换硬盘后重启会自动更新硬盘挂载点。因不少朋友用的是多分区硬盘，且本版固件在web管理界面能清楚地查看硬盘挂载点及分区大小，所以取消了Transmission自动更新挂载点的功能，改为全手动输入。
4、至此，下载宝已基本完成了下载器+视频服务器的功能定位。
    1）下载器：迅雷下载、PT下载、百度云同步，后续不再加入aria2等其他下载功能。
    2）视频服务器：NFS、DLNA、Samba。

**安装方法：**1、升级160529版固件，方法详见下方**【固件升级】**。
2、下载160529版opt运行包，将“opt”目录解压到SD卡上，即在SD卡根目录下建有“opt”文件夹。SD卡最好是NTFS格式。
3、将SD卡插入下载宝。
4、重启下载宝，即可正常使用。
5、若不需要Transmission和百度云同步功能，则不必安装SD卡。
**百度云同步配置方法：**

1、进入：服务/SyncY

 

![img](/Users/maple/code-try/blog-backups/source/_posts/assets/094003vu2kxk4v06hj9k1v-20190429182804262.png) 

 

2、绑定百度账号

 

![img](/Users/maple/code-try/blog-backups/source/_posts/assets/001432shhhah989f8hhfah-20190429182804263.png) 

 

3、按提示操作，百度账号授权成功后返回此界面，点击“继续账号绑定”

 

4、账号绑定成功

 

![img](/Users/maple/code-try/blog-backups/source/_posts/assets/001439fj0p550j85p69v4j-20190429182804264.png) 

 

5、

设置好各参数及同步目录，“保存&应用”。云端同步目录请设为“/apps/SyncY”，必须事先在百度网盘上的“我的应用数据”下建立“SyncY”目录（注意大小写）。
![img](/Users/maple/code-try/blog-backups/source/_posts/assets/002229ogqsmm77kml0p5pq-20190429182804265.png) 
6、点击：“启动”，百度云同步在后台运行。

 

![img](/Users/maple/code-try/blog-backups/source/_posts/assets/001440lvr7aw7ww1ava3ww-20190429182804265.png) 

 

 

当所有内容同步完成之后，经过设定的同步间隔周期才会自动再次同步。若需要在同步间隔周期之内立即开始执行同步，则须手动“停止”+“启动”。

7、详细参数设置请参阅syncy官方博客：<http://www.syncy.cn/index.php/syncyconfighelp/>，感谢作者所做出的杰出贡献！

 

【5月23日更新】

加入防火墙的51413端口规则，以确保Transmission的正常下载。

 

安装方法：

 

1、升级160523版固件，更新方法详见下方。

 

2、下载opt文件包，连同“opt”目录名解压到SD卡上。就是说，在SD卡根目录下建有“opt”文件夹。将SD卡插入下载宝。

 

3、重启下载宝，即可正常使用。

 

4、若不需要Transmission，则不必安装SD卡。

 

 

Transmission配置：

 

在默认

配置下，登陆

http://www.canyouseeme.org/

，查看51413端口是否已打开。若已正常打开，则忽略以下步骤。若显示未打开端口，则进行如下配置：

 

1、打开Transmission的web管理页面，如下图勾选设置选项，51413端口请勿改动。

 

![img](/Users/maple/code-try/blog-backups/source/_posts/assets/214206bjwhzjadztakt4a4-20190429182804266.jpg) 

 

 

 

2、在路由器里打开UPnP选项，且手动添加51413（TCP和UDP）端口转发规则。

 

3、再次

登陆

[http://www.canyouseeme.org/](https://www.right.com.cn/forum/qq://txfile/)，查看51413端口是否打开。若已打开，恭喜你可享用pt下载了。若还是不能打开端口，则意味着你的网络运营商封杀了51413端口或者你的路由器是处于一个局域网之中。前者可尝试改动端口号或者让Transmission随机分配端口号，但不保证能正常下载。后者可在局域网的公网入口端的路由设备上开通UPnP或设置51413端口映射。

 

【5月21日更新】

 

１、SD卡外挂optware运行环境

 

​    下载宝虽然主频和运存都还不错，但闪存只有16M，加入NFS之后，已经几乎没有折腾空间了。因此，扩展了SD卡作为存储介质，弥补了闪存的不足。对于SD卡没有特别要求，只要能被下载宝识别，且容量大于32M即可。

 

2、T

ransmission：

 

​    高清爱好者当然离不开Transmission。就此，NFS+Tr双剑合璧。

 

​    1）最新版2.92-2。

 

​    2）加入最新版原生中文的专业web管理器Transmission Web Control 1.0 Beta（20141010），可在

**Web Control、手机移动UI、原版UI**

之间自由切换，功能不可谓不强大！默认web管理页面：http://下载宝ip地址:9091

 

​    3）更换移动硬盘后重启下载宝，默认下载位置自动变更为当前硬盘。须事先在硬盘根目录创建

**“transmission”文件夹**

。

 

![img](/Users/maple/code-try/blog-backups/source/_posts/assets/154844vum75ka288n7m2kn-20190429182804267.png) 

 

 

3、提供了扩展选项功能

 

​    固件的基本版本默认开启SSH和NFS，同时提供硬盘禁止APM的版本。而Transmission以及后续陆续加入的aria2、百度云同步等功能，将采用SD卡配置文件的方式由使用者自行决定是否开启相应功能。

 

 

安装方法：

 

1、升级160521版固件，更新方法详见下方。

 

2、下载opt文件包，连同“opt”目录名解压到SD卡上。就是说，在SD卡根目录下建有“opt”文件夹。将SD卡插入下载宝。

 

3、重启下载宝，即可正常使用。4、若不需要Transmission，则不必安装SD卡。

 

 

【历史版本说明】

 

 

【5月19日首发】

 

目前暂定通用功能：

 

1）保留官方固件所有功能。之所以用下载宝，就是要用其专用下载模块，否则就用不再维护更新的xware好了。

 

2）开放ssh：用户名和密码均为admin，感谢“三水良子”大神提供基础固件！

 

3）NFS服务：对于全高清蓝光爱好者而言，NFS是网播的不二选择。官方提供的samba效率太低，dlna不支持蓝光原盘也不支持外挂字幕。官方固件基于pandorabox进行开发，内核已封闭，因此无法安装内核级的nfs服务，只能安装基于用户空间的NFS（unfs3），经实测百兆有线局域网和无线5G中继播放蓝光3D原盘流畅无压力。

 

 

在通用功能基础上，增加了一个禁止硬盘APM的版本。下载宝在下载速度比较低的时候，正常情况下硬盘磁头会频繁复位，导致噪声恼人，也大幅提高了硬盘的c1值，影响硬盘寿命。当然，禁止APM之后，硬盘处于全速运行，发热量和功耗都会稍大。禁止APM后，下载宝上的硬盘弹出按键照常使用。

 

 

【固件升级】

 

以下二者取一
1）登陆：http://下载宝ip:81/upgrade.do，选择固件，应用升级即可。318版以后的固件端口改为：8800。
2）关机断电后，用工具按住reset键，上电，待led灯频繁闪烁之后松开。有线连接电脑，登陆：[http://192.168.1.1](http://192.168.1.1/)，选择恢复固件，自动重启后即可使用。
整个固件升级过程需要三分钟左右，请耐心等待。在此过程中切勿断电！刷机有风险，刷者后果自负。

 

建议提前刷H大的breed，<https://www.right.com.cn/forum/thread-161906-1-1.html>，下载宝专用版：[breed-mt7621-xunlei-timeplug.bin](http://breed.hackpascal.net/breed-mt7621-xunlei-timeplug.bin)。
刷breed：用上述第2种方式，选择下载好的专用版breed，恢复即可。将来若想恢复原厂uboot，可在breed里选择“固件更新/Bootloader”刷入。
启动breed：按住硬盘弹出键（而非reset键），然后上电，待led等频繁闪烁之后松开。

 

**【固件下载】**

 

338复活版

链接: <https://pan.baidu.com/s/1pNokoKb> 密码: i4js

 

327终极版
链接: <https://pan.baidu.com/s/1eSPRlb8> 密码: mbk2

 

官方uboot

 

链接：

http://pan.baidu.com/s/1mh6FwnA

 密码：m52r