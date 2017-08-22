---
title: PHP分词工具SCWS的本地安装方法
date: 2015-12-07 15:30:48
tags: [PHP, 分词, SCWS]
---
&emsp;&emsp;在做信息检索的大作业的时候，因为需要对一段文本进行向量化，因此需要进行中文的分词，我就偷了个懒，在网上搜索了一下，找到了一个比较好用的php分词工具——scws。还是开源的哦^_^  
&emsp;&emsp;刚开始尝试使用的时候我是使用了它提供的在线分词的API，就只需要将要分词的语句采用POST的方式发送到它指定的API接口地址，就能返回一个数组，这个数组里面存放的是该语句分词的结果。而且可以自己选择返回结果采用json还是xml等方式。非常的方便实用。 

<!-- more -->

&emsp;&emsp;当然，在线分词最大的缺点就是速度比较慢，因为所有语句都需要发送到那个接口地址。因此我又根据它官方的文档在本地安装了这个工具及其php扩展。下面是安装方法：  
# 以Ubuntu为例。
# 1. 安装scws
## 1.1 从官网获取源码
&emsp;&emsp;下载源代码包，下载后解压到某一目录下(如~/Coding/scws-1.2.2)。  
## 1.2 编译安装
&emsp;&emsp;在终端下cd进入上述解压缩后的目录，依次执行一下命令：

    $ ./configure --prefix=/usr/local/scws
    $ make
    $ sudo make install

&emsp;&emsp;注：这里和通用的 GNU 软件安装方式一样，具体选项参数执行 ./configure –help 查看。  
&emsp;&emsp;常用选项为：–prefix=< scws的安装目录 >  
&emsp;&emsp;顺利的话应该已经安装成功了，可以使用下面的命令来运行一下scws-cli文件：

    $ /usr/local/scws/bin/scws -h

如果看到下面这样的信息就代表安装成功了。

    scws (scws-cli/1.2.2)
    Simple Chinese Word Segmentation - Command line usage.
    Copyright (C)2007 by hightman.
    ...

## 1.3 安装词典

    $ cd /usr/local/scws/etc
    $ wget http://www.xunsearch.com/scws/down/scws-dict-chs-utf8.tar.bz2
    $ tar xvjf scws-dict-chs-utf8.tar.bz2

# 2. 安装php扩展以便在php中调用分词
&emsp;&emsp;进入源码目录下的phpext/目录下(如cd ~/Coding/scws-1.2.2/phpext)，执行以下命令：

    $ phpize        #可能需要先执行 sudo apt-get install php5-dev 来安装phpize
    $ ./configure --with-scws=/usr/local/scws 
    $ make
    $ sudo make install

此处执行完毕后会出现如下字符：

    Installing shared extensions:     /usr/lib/php5/20131226/

将此目录地址复制下来留用。  
&emsp;&emsp;在php.ini文件中加入下面的文字：

    [scws]
    ;
    ; 注意请检查 php.ini 中的 extension_dir 的设定值是否正确, 否则请将 extension_dir 设为空，
    ; 再把 extension = scws.so 指定绝对路径。
    ;
    extension = /usr/lib/php5/20131226/scws.so
    scws.default.charset = utf8
    scws.default.fpath = /usr/local/scws/etc

而后只要重新载入php.ini即可（如果是使用php-fpm那么可以执行 sudo /etc/init.d/php5-fpm restart来重新载入；如果是使用php-cli那么只要修改了php.ini下次调用时会自动重新载入的）。
