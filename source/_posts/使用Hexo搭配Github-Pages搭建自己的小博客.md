---
title: 使用Hexo搭配Github Pages搭建自己的小博客
date: 2015-7-29 19:25:48
tags: [博客, Hexo]
---
&emsp;&emsp;在使用新浪博客写第一篇博客的时候我就发现，新浪博客的网页编辑器有许多小的问题，比如格式在编辑的时候和最终发不出去的可能会有细微的差别，又或者编辑公式和代码都挺不方便的。  
&emsp;&emsp;因此就很容易想到Latex这种东西，但是在网上查了一番，都没有查到能够使用Latex来写博客的平台或者框架什么的。而由同学的推荐，我得知了一个神奇的框架：Hexo。  
&emsp;&emsp;Hexo是出自于台湾大学生tommy351之手的一套基于Node.js的静态博客框架。说它是框架其实它更像是一个能将Marddown文档转化成HTML静态页面的一整套程序，或者说这算得上是一个编译器了？  
&emsp;&emsp;闲话少说，下面一起来看一下怎么使用Github Pages服务搭配Hexo来建立属于自己的博客吧。  

<!-- more -->

# 注意，本文假定你已经具备基础的git、vim等软件使用能力！
# 1. 安装各种程序
## 1.1 Node.js安装
&emsp;&emsp;官网上并没有支持Ubuntu平台下安装Node.js的apt源，因此我们使用NVM工具来安装nodejs。只需要具体查查nvm的使用方法即可。  
## 1.2 安装Hexo
&emsp;&emsp;打开终端，执行以下指令：

    $ sudo npm install hexo-cli -g

# 2. 建立自己的博客
## 2.1 初始化博客项目
&emsp;&emsp;在你想要保存该项目的位置（如home目录下），输入以下命令初始化：

    $ hexo init testHexo

其中testHexo是你想初始化的项目的文件夹的名称。当然你也可以直接cd到该目录下，然后执行 hexo init 即可。
## 2.2 选择一个喜欢的主题
&emsp;&emsp;Hexo的一大亮点就是它能够让你轻松替换掉整个博客的主题。你可以去这里找找看你喜欢的主题，每个主题都有相应的demo。假设我们这里要把Hexo设置为Yilia主题（当然你也可以用别的主题），那么我们找到它的项目页面，就可以获取其clone地址。然后打开终端：

    $ cd themes
    $ mkdir yilia
    $ cd yilia
    $ git clone git@github.com:litten/hexo-theme-yilia.git

在这个主题的目录下我们会发现也有一个_config.yml文件，这个文件就是用来设置主题中具体细节的文件了。  
&emsp;&emsp;我们打开这个_config.yml文件：

    $ vim _config.yml

这个文件中很多东西都是我们可是自己设置的。但我们大体上需要设置以下几个方面：  
  1. subnav项中我们可以设置一些快速连接到我们的其它的社交网站主页，比如新浪微博、Github等。只要把相应的主页网址填上即可。不需要的可以用#号注释掉。  
  2. avatar项可以设置你的博客主页上的头像，我这里设置的是新浪微博头像相册中的图片，因此这个地方填的是它的url（不知道可不可以使用本地的，没测试～）。  
  其余的设置可以读者可以自行参考相应主题的说明来修改，这里不再赘述。  
&emsp;&emsp;然后我们还要在Hexo的_config.yml中修改一些设置：

    $ cd ../..                  #切换到yilia目录下
    $ vim _config.yml

打开文件以后，按照下面的说明来修改吧：

    # Hexo Configuration
    ## Docs: http://hexo.io/docs/configuration.html
    ## Source: https://github.com/hexojs/hexo/

    # Site
    title: 、对月笙歌                     #这里是博客的名字
    subtitle: 樽天养性，对月笙歌           #博客的副标题，类似于个性签名吧
    description:
    author: 对月笙歌                      #作者
    language:
    timezone:

    # URL
    ## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
    url: http://tevenfeng.github.io        #这里设置的网址是你博客要上传的地方，比如我这里上传到github上，就填写我的地址。
    root: /
    permalink: :year/:month/:day/:title/
    permalink_defaults:

    # Directory
    source_dir: source
    public_dir: public
    tag_dir: tags
    archive_dir: archives
    category_dir: categories
    code_dir: downloads/code
    i18n_dir: :lang
    skip_render:

    # Writing
    #这里可以设置你新发表文章时所使用Markdown文件的名字，我这里使用"年-月-日-名字.md"的形式，方便后期管理
    new_post_name: :year-:month-:day-:title.md
    default_layout: post
    titlecase: false # Transform title into titlecase
    external_link: true # Open external links in new tab
    filename_case: 0
    render_drafts: false
    post_asset_folder: false
    relative_link: false
    future: true
    highlight:
      enable: true
      line_number: true
      auto_detect: true
      tab_replace:

    # Category & Tag
    default_category: uncategorized
    category_map:
    tag_map:

    # Date / Time format
    ## Hexo uses Moment.js to parse and display date
    ## You can customize the date format as defined in
    ## http://momentjs.com/docs/#/displaying/format/
    date_format: YYYY-MM-DD
    time_format: HH:mm:ss

    # Pagination
    ## Set per_page to 0 to disable pagination
    per_page: 10
    pagination_dir: page

    # Extensions
    ## Plugins: http://hexo.io/plugins/
    ## Themes: http://hexo.io/themes/
    theme: yilia               #这里设置你要使用的主题的文件夹名字，比如我这里是yilia

    # Deployment
    ## Docs: http://hexo.io/docs/deployment.html

    #这里设置你要部署的地方，比如我这里要部署到github上。
    deploy:
      type: git
      repo: git@github.com:tevenfeng/tevenfeng.github.io.git

&emsp;&emsp;样子你的博客就设置得差不多了。
## 2.3 发布新文章
&emsp;&emsp;首先你可以使用以下命令来创建新的文章：

    $ hexo new "这是一篇测试文章"

&emsp;&emsp;然后它会提示在某个目录下创建了一个名为”这是一篇测试文章.md”的文件，找到这个文件，然后你就可以开始用Markdown来写博客了。
# 本地测试
&emsp;&emsp;我们可以在本地使用服务器来预览一下自己写的文章，具体方法如下：

    $ npm install hexo-server --save         #在整个项目的根文件夹下安装hexo的服务器
    $ hexo server

然后打开浏览器，输入 localhost:4000，就可以看到我们的博客在本地运行了。
# 部署
&emsp;&emsp;那么我们如何将博客部署到Github上做为Github Pages来运行呢？方法如下。  
&emsp;&emsp;由于我们在设置文件中设置好了部署的位置和类型，因此我们只要在项目根目录下打开终端然后输入一下命令即可完成部署和生成:  

    $ hexo deploy -g

&emsp;&emsp;至此，整个博客搭建的基本过程就结束了。