---
title: 宝塔 WebHook 使用教程
tags: 技术
date: 2018-11-02 10:07
comments: true
---

最近开发一个微信的项目，需要配置服务器地址来接管消息处理，不能在像以前那样本地开发了，但也不能直接修改线上服务器的代码吧？这时候就要WebHook登场了。  

简单介绍一下什么是WebHook：
> Webhook是一个API概念，并且变得越来越流行。我们能用事件描述的事物越多，webhook的作用范围也就越大。Webhook作为一个轻量的事件处理应用，正变得越来越有用。  

> 准确的说webhook是一种web回调或者http的push API，是向APP或者其他应用提供实时信息的一种方式。Webhook在数据产生时立即发送数据，也就是你能实时收到数据。这一种不同于典型的API，需要用了实时性需要足够快的轮询。这无论是对生产还是对消费者都是高效的，唯一的缺点是初始建立困难。  

> Webhook有时也被称为反向API，因为他提供了API规则，你需要设计要使用的API。Webhook将向你的应用发起http请求，典型的是post请求，应用程序由请求驱动。

接下来就来试试如何使用宝塔面板的WebHook插件吧！

1.1 安装Git
---

首先需要在你的服务器上安装git，登录服务器，执行 ```git --version```命令查看是否安装。  
如果没有，执行```yum install git```安装git

1.2 安装WebHook
---

进入宝塔面板，依次进入：软件管理->宝塔插件，在列表里可以看到宝塔WebHook插件，点击后面的安装即可。

1.3 添加WebHook
---

<img src="https://cdn.wispx.cn/blog/2018/11/02/cd9c402f9c82cbf7.png" width="100%">
这里先随便输入一个符号(在编辑的时候重新添加shell命令进去，在上图输入框输入的命令会被过滤)
点击提交后，在编辑，输入下面的脚本：
```shell
#!/bin/bash
echo ""
# 输出当前时间
date --date='0 days ago' "+%Y-%m-%d %H:%M:%S"
echo "Start"
# 判断宝塔WebHook参数是否存在
if [ ! -n "$1" ];
then 
          echo "param参数错误"
          echo "End"
          exit
fi
# git项目路径
gitPath="/www/wwwroot/$1"
# git 网址
gitHttp="http://git.xxxxx.com/$1.git"

echo "Web站点路径：$gitPath"

# 判断项目路径是否存在
if [ -d "$gitPath" ]; then
        cd $gitPath
        # 判断是否存在git目录
        if [ ! -d ".git" ]; then
                echo "在该目录下克隆 git"
                git clone $gitHttp gittemp
                mv gittemp/.git .
                rm -rf gittemp
        fi
        # 拉取最新的项目文件
        git reset --hard origin/master
        git pull
        # 设置目录权限
        chown -R www:www $gitPath
        echo "End"
        exit
else
        echo "该项目路径不存在"
        echo "End"
        exit
fi
```
添加完成以后，查看密钥
<img src="https://cdn.wispx.cn/blog/2018/11/02/8ea1a87a68c31d01.png" width="100%">

组成的链接是这样的：
http://面板Ip加端口/hook?access_key=密钥&param=项目在/www/wwwroot/目录下的目录

> 说明：当前shell命令把目录作为了变量 param 传输 考虑多项目的情况

1.4 配置git WebHook钩子
---

由于我使用码云比较多，这里就使用码云来做示例。
<img src="https://cdn.wispx.cn/blog/2018/11/02/4b93202b4aa335cf.png" width="100%">

点击提交后，测试：
<img src="https://cdn.wispx.cn/blog/2018/11/02/9ed067a1d3aef514.png" width="100%">

完成！

私有项目如何配置？
---

以上使用的是公开的仓库，所有人都有拉取代码的权限，那如果是私有项目如何通知服务器拉取呢？  
这时候就需要用到项目部署公钥(SSH)了。

2.1 生成SSH公钥
---

首先登录服务器
输入命令```cd ~/.ssh && ls```查看有没有SSH key
<img src="https://cdn.wispx.cn/blog/2018/11/02/1777101a6151226a.png" width="100%">
如果没有，使用命令```ssh-keygen -t rsa -C "your_email@example.com"```生成。如下图所示：
<img src="https://cdn.wispx.cn/blog/2018/11/02/e0f2bd578e69751d.png" width="100%">

复制好id_rsa.pub文件内容后，打开码云的私有项目，依次点击：管理->添加公钥，如图：
<img src="https://cdn.wispx.cn/blog/2018/11/02/6226f031fbb2aee6.png" width="100%">

在码云上添加好公钥后，使用```ssh -T git@gitee.com```测试SSH连接：
<img src="https://cdn.wispx.cn/blog/2018/11/02/6193794123d6277b.png" width="100%">

2.2 更改shell命令中的git地址
---

复制码云私有项目仓库的SSH连接地址，如图：
<img src="https://cdn.wispx.cn/blog/2018/11/02/ab1256027e8bfddc.png" width="100%">
将shell命令中的git地址更改为：```gitHttp="你复制的SSH链接"```。

完成！

