---
title: Ubuntu15.04下安装LNMP套件
date: 2015-07-28 21:28:04
tags: [LNMP, php]
---
&emsp;&emsp;LNMP是Linux+Nginx+MySQL+PHP的简称，是一套完整的PHP网站服务器架构环境。  
&emsp;&emsp;说明：本文所用Linux发行版为Ubuntu Gnome 15.04，所有需要的软件均使用Ubuntu自带apt源下载安装。 

<!-- more -->

# 1 各种软件安装
## 1.1 准备工作
&emsp;&emsp;为了确保你的Ubuntu软件源是最新的，所以你需要先使用以下命令更新下你的软件源：

    $ sudo apt-get update

## 1.2 安装nginx

    $ sudo apt-get install nginx

&emsp;&emsp;等待nginx安装完成后，打开浏览器，在地址栏输入localhost，检测nginx是否开启；若开启成功，则会显示欢迎使用Nginx的界面；若没有，则在终端下输入以下命令即可打开nginx：

    $ sudo /etc/init.d/nginx start

## 1.3 安装MySQL

    $ sudo apt-get install mysql-server-5.6 mysql-workbench mysql-client-5.6

&emsp;&emsp;安装过程中需要设置root用户的密码，选择自己容易记住的密码即可。  
## 1.4 安装PHP

    $ sudo apt-get install php5 php5-fpm php5-mysql php-apc php5-cgi php5-cli

&emsp;&emsp;至此所有基本组件已安装完毕，当然你也可以根据需要继续安装其它可选组件等。  
# 2 配置
## 2.1 配置Nginx对PHP的设置
### 2.1.1 对default文件的修改
&emsp;&emsp;首先修改sites-available目录下的default文件：

    $ sudo vim /etc/nginx/sites-available/default

&emsp;&emsp;在打开的文档中找到root一项（在30行附近），将其修改为：

    root /home/tevenfeng/coding/php/tjurobocup

&emsp;&emsp;同时找到index一项（也在30行附近），将其修改为：

    index index.html index.htm index.debian.html index.php

&emsp;&emsp;最后找到以下代码块，按照样子修改（其实就是去掉一些“#”号然后加一句话）：

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000

    #

    location ~ \.php$ {

        #include snippets/fastcgi-php.conf;

        ## With php5-cgi alone:

        #fastcgi_pass 127.0.0.1:9000;

        ## With php5-fpm:

        fastcgi_pass unix:/var/run/php5-fpm.sock;

        include fastcgi_params;
    }

&emsp;&emsp;然后保存即可。
### 2.1.2 对fastcgi_params文件的修改

    $ sudo vim /etc/nginx/fastcgi_params

 在该文件末尾添加如下行：

    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

&emsp;&emsp;保存退出即可  
## 2.2 配置PHP

    $ sudo vim /etc/php5/fpm/php.ini

&emsp;&emsp;找到(大约在700行附近)：

    :;cgi.fix_pathinfo()=1

&emsp;&emsp;将其修改为：

    cgi.fix_pathinfo()=0        //将注释去掉并修改值为0

&emsp;&emsp;找到（720行附近）：

    doc_root =

&emsp;&emsp;将其修改为：

doc_root = /home/tevenfeng/coding/php/tjurobocup

&emsp;&emsp;注意！！！此处的路径应与前面修改的default文件中的root一项的路径一致！此处也可不填任何路径。  
&emsp;&emsp;保存并退出即可。至此所有基本配置完成。
# 3. 重启Nginx和php5-fpm：

    $ sudo /etc/init.d/nginx restart
    $ sudo /etc/init.d/php5-fpm restart

&emsp;&emsp;此时，若你在源代码文件夹下放有index.php文件，则打开浏览器输入localhost应该能打开相应的界面了，说明此时LNMP开发环境配置成功。