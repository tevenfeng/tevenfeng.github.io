---
title: 网件WNR2000v4在深度操作系统下ipv6的小问题
date: 2016-10-15 20:39:57
tags: [Deepin, ipv6, 网件]
---
&emsp;&emsp;天大校园网是以太网可以自动获取原生ipv6地址，因此在使用Netgear的路由器时，可以在ipv6管理中开启穿透模式，然后重启路由器，就能发现Windows系统是可以自动获取到ipv6的地址的。  
&emsp;&emsp;我的deepin系统能够获取到ipv6地址，但是无法访问六维空间，而且也无法ping通ipv6的地址，所以想了想应该是系统无法获取正确的默认路由（网关），可以手动添加个默认网关。在windows下查询到默认网关为：

    fe80:355::1

&emsp;&emsp;当然不一定都是这个，需要根据路由器分配的来定，所以需要查看windows下的默认网关。  
&emsp;&emsp;因此使用下面的命令添加：

    ip route add 2001::/3 via fe80:355::1 dev enp4s0f0

&emsp;&emsp;因为我们学校获取的ipv6地址是2001:开头的，所以add后面需要跟2001::/3，然后via后跟默认网关地址，dev后跟网络的接口名。  
&emsp;&emsp;为了开机启动以后就能自动添加默认网关，所以可以把这句话添加到/etc/rc.local里面，注意ip这个程序要写完整路径即/bin/ip。  
&emsp;&emsp;这样子我的deepin就可以用ipv6了。而且经过测试，Ubuntu经过这样子设置以后也是可以的。  
