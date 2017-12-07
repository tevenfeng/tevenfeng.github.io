---
title: 解决Ubuntu无法使用A2DP蓝牙模式的问题
date: 2017-12-07 13:39:29
tags: [Ubuntu, 蓝牙, A2DP]
---
&emsp;&emsp;买了蓝牙耳机以后发现Ubuntu默认连接蓝牙耳机的时候用的是HSF模式，然后音质渣得就像是在听上个世纪不知道哪个年代的收音机一样。于是各种在Google寻找解决方法。最后终于找到了一个方法。
&emsp;&emsp;解决方法就是在/var/lib/gdm3/.config/pulse/目录下新建一个default.pa的文件，然后在这个文件下输入以下内容：

    #!/usr/bin/pulseaudio -nF
    #

    # load system wide configuration
    .include /etc/pulse/default.pa

    ### unload driver modules for Bluetooth hardware
    .ifexists module-bluetooth-policy.so
    unload-module module-bluetooth-policy
    .endif

    .ifexists module-bluetooth-discover.so
    unload-module module-bluetooth-discover
    .endif

然后重启电脑即可。