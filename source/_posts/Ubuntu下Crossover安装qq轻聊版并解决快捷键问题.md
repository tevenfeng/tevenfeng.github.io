---
title: Ubuntu下Crossover安装qq轻聊版并解决快捷键问题
date: 2017-02-23 16:38:34
tags: [Crossover, QQ, Wine]
---
&emsp;&emsp;在上课的时候无聊，于是想着来解决一下qq的问题，虽然之前都是用的wineqq-intl，也就是国际版的。但是由于它本身很老了，导致手机在线的时候聊天时也会不停地响。所以想安装个新版本的qq。  
&emsp;&emsp;于是想到了深度的解决方案：用crossover来安装轻聊版的qq，还没有广告，多爽。  
&emsp;&emsp;因此在网上下了个crossover16，然后安装轻聊版的qq。可以正常登录。  
&emsp;&emsp;但是问题来了，这个qq在最小化以后，无法使用ctrl+alt+z的快捷键来唤起qq主程序或者提取消息。于是我就找了下深度的解决方案，发现深度是用的一个叫做qq-sendkey.exe的helper程序来向运行中的qq进程来传递快捷键参数。于是在网上找到了对应的qq-sendkeys.exe和qq-sendkeys.sh文件（就存在我的onedrive里面备用啦）。  
&emsp;&emsp;然后将这两个文件放到~/.local/cxoffice/文件夹下，然后修改qq-sendkeys.sh文件中的路径即可。而后在Ubuntu中添加自定义快捷键调用qq-sendkeys.sh即可。  
&emsp;&emsp;最后测试，完美解决。那么同样的应该可以通过这个程序来传递截图的快捷键参数进去，就可以实现调用qq的截图啦。