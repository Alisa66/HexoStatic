---
title: How to deploy our own Internet service
date: 2018-04-19 15:53:46
tags: 服务器，service
---
# Nodejs+宝塔+centos7服务器搭建

首先特别声明！！！小白的定义是什么都不懂。这里只是我的个人理解。如果有错误，可以指出，看你怎么指出了。改不改就是我的事情了。不接受恶意评论。爱看不看。脾气就是这么不好。这只是我个人这两天搭建服务器和网站的经历。记录一下，说不定能帮到其他人。毕竟也是google别人贴出来的，才能够搭建出来。

# 什么是服务器？

1. 当我们浏览别人的网站的时候，这些网站是存放在哪里的？为什么我们不论在哪台电脑上输入这个网址都能访问到这个网站。这个存放网站的地方就叫服务器。服务器就相当于一台虚拟电脑。和我们本地的电脑差不多，还是的有一些基本的配置。既然是电脑，你总得花钱买吧，有很多卖服务器的，自己看个人的选择，看了好多人都推荐不在国内买，原因是不稳定还是什么。我是在这个[https://virmach.com/](https://virmach.com/)网站上面买的。买了之后会给你一个ip，uersname，和password。
1. 买的时候就会让你自己选择配置和系统了。买好了就自动给你装上了。

# 搭建服务器需要准备的工具

服务器可以用很多工具搭建。我用的是node+nginx.

1. 要下载安装xshell ！  前面说了服务器就相当于一台虚拟电脑，你得先在本地写了东西，然后上传上去。那么连接你本地和服务器之间就需要一个工具了。这就是xshell。官方定义是：Xshell是一款功能强大且安全的终端模拟器。通俗一点就是连接服务器的。连接的时候需要输入我们上一步所购买的服务器的相关信息。 如果xshell 显示下图，就说明连接成功了。但是现在服务器里面什么都没有。服务器的系统一般都是linux。我也不懂。

  ![](https://www.notion.so/file/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F8a047bad-b67e-43c6-9b0c-56a02e1aa83a%2FUntitled)

1. 在xshell里面用命令行安装宝塔！！！现在你就可以网服务器里面装你需要用的东西了。但是如果你用命令行装的话，会非常麻烦，所以就用一个工具帮我们装。现在的xshell就相当于我们本地的控制台了，这个xshell就是我们服务器里面的控制台。这里我用的是宝塔bt.cn。在xshell中输入命令     yum install -y wget && wget -O [install.sh](http://install.sh/) [http://download.bt.cn/install/install.sh](http://download.bt.cn/install/install.sh) && sh [install.sh](http://install.sh/)     不同的系统安装语句不一样，自己去宝塔的官网找到对应的安装语句，输入到xshell。  安装好了会自动给你一个宝塔的用户名和密码。打开宝塔面板登陆上去就好了
1. 这个宝塔就是一个管理工具，帮助我们安装其他的工具，我们就不用再xshell中在输入命令，像安装宝塔一样。里面有软件管理。按照自己的需要选择安装。这里 安装的软件就在我们的服务器里面了，宝塔就是相当于服务器里面的一个界面。里面要装上ngix和mysql。
1. 在本地下载安装node和git。网上教程一大堆，这里就不详细讲了。

# 测试

1. 新建一个something.js。里面内容使用nodejs写的。然后在xshell中启动这个文件 npm start 你的文件名。  在浏览器中输入你的ip和你js文件中监听的端口号。如果输出了，就说明成功了。

# 再次安装

1. xshell 我们不可能一直开着，现在我们一关闭xshell，这个js就会停止运行。别人访问我们的ip就无法访问到。所以现在用xshell安装一个***forever***。安装了forever之后，当我们关闭xshell的时候，我们的js还会照样运行，别人访问的时候也能看见。
1. 设置nginx。nginx是方向代理。我理解的是，你要用服务器干啥事，人家不一定想要见你，所以就让nginx来代替他来完成你委托他的事情。在宝塔面板里面设置一下nginx。代码如下。

# 设置域名

现在的ip地址不好记，所以我们要用简短的文字母。这就是域名了。反正我是这么理解的。先去网上买一个域名。我购买的网址是free什么。买的时候尽量将域名取得短一点，太长了不好记住。买好了之后问题就来了，怎么把你当前服务器的ip指向你的域名？

在宝塔面板里面的站点设置网站 如下图所示，将你的域名添加进去。提交之后点击设置

![](https://www.notion.so/file/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fdad93217-e3e6-4d4d-8f83-9ff5426f6e1d%2FUntitled)

如下图所示，再打开的设置里面的配置文件中，设置nginx反向代理、

location / {
proxy_pass [http://127.0.0.1:8080](http://127.0.0.1:8080/);
proxy_http_version 1.1;
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "upgrade";
proxy_set_header Host $host;
}

![](https://www.notion.so/file/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fde3590c5-12c1-40a5-a882-c695e3770dd2%2FUntitled)

这样一来。nginx就配置好了。但是这样还没有完。我们当前的ip并没有指向我们的域名。在宝塔里面安装一个云解析。

1. 点击设置，然后添加域名，然后输入我们的域名。点击添加之后会出现这样的

   

![](https://www.notion.so/file/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F81ee52b6-d633-43d7-9373-a0b6b41c4fd2%2FUntitled)

在我们购买ip域名的网址里面 找到 然后到域名注册网站将域名NS修改为下面两项。

![](https://www.notion.so/file/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F826f2b57-c863-4369-a973-a85918f06d75%2FUntitled)

![](https://www.notion.so/file/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fd9e44efd-b901-49fa-9a54-b0d963d6ad4e%2FUntitled)

到这里为止。我们整个网站就搭建好了。可以输入域名测试一下了