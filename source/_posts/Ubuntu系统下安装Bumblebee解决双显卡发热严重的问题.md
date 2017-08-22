---
title: Ubuntu系统下安装Bumblebee解决双显卡发热严重的问题
date: 2016-01-03 17:32:12
tags: [Ubuntu, Bumblebee, 双显卡]
---
&emsp;&emsp;因为我笔记本是nvidia和英特尔双显卡配置，所以在使用ubuntu系统的时候会出现两个显卡同时开着，浪费电也就算了，关键是发热量还巨大无比。  
&emsp;&emsp;以前我在用Ubuntu12.04的时候曾经配置过Bumblebee来关闭独立显卡，所以这就用得上了。  

<!-- more -->

&emsp;&emsp;现在用的是Ubuntu14.04，官方仓库里面有Bumblebee了，所以不需要另外添加源就可以安装了。  

    $ sudo apt-get install bumblebee bumblebee-nvidia primus linux-headers-generic

&emsp;&emsp;但是发现它安装的驱动是304的驱动，有点老，所以就自己安装新版的驱动:

    $ sudo apt-get install nvidia-352-updates           #这里的驱动可以换成别的版本

&emsp;&emsp;安装完以后还要设置一下，不然开机会黑屏无法进入桌面，而且在调用optirun的时候会报错说无法载入驱动。

    $ sudo vim /etc/bumblebee/bumblebee.conf

找到

    TurnCardOffAtExit=false

改成

    TurnCardOffAtExit=true

找到

    Driver=

改成

    Driver=nvidia

找到

    KernelDriver=

改成

    KernelDriver=nvidia

保存关闭文件。

    sudo vim /etc/modprobe.d/bumblebee.conf

在该文件的末尾添加（注意：前面安装的驱动版本是多少这里就填多少，如我这里是nvidia-352）：

    blacklist nvidia-352

保存关闭文件。  
&emsp;&emsp;重启以后应该能正常进入桌面，此时独显应该已经被关闭了。如若未被关闭，则去nvidia设置软件里面切换成集显即可。若某软件需要调用独显则使用sudo optirun XXX就可以了。
