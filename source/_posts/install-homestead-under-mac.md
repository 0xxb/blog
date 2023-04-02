---
title: Mac 下安装 Homestead
tags: 技术
date: 2018-04-18 09:13
comments: true
---

闲起来又开始爬坑了，有坑往里钻，有问题就慢慢扒，不得不说越来越喜欢这感觉了，感觉时间根本不够用。

官方的教程果然都是有坑的，让人头皮发麻。

本篇文章只是搭建Homestead教程，就不详细的介绍遇到的问题了。

注意：本篇文章只针对mac用户，windows请移步其他教程。

### 简单介绍一下Homestead
Homestead是laravel为开发者提供的重量级本地开发环境。
> Laravel Homestead 实际是一个打包好各种 Laravel 开发所需软件和工具的 Vagrant 盒子（关于 Vagrant 盒子的释义请参考 Vagrant 官方文档），该盒子为我们提供了一个优秀的开发环境，有了它，我们不再需要在本地环境安装 PHP、Composer、Nginx、MySQL、Memcached、Redis、Node 等其它工具软件，我们也完全不用再担心误操作搞乱操作系统 —— 因为 Vagrant 盒子是一次性的，如果出现错误，可以在数分钟内销毁并重新创建该 Vagrant 盒子！

### 安装VirtualBox、Vargrant
VirtualBox5.2.10 下载地址：

https://www.virtualbox.org/wiki/Downloads

Vargrant 下载地址：

https://www.vagrantup.com/downloads.html

验证是否安装成功在终端使用以下命令行，显示版本信息就可以了<br>
```bash
vagrant -v
```

除了VirtualBox你还可以选择[VMWare](https://www.vmware.com/ "VMWare")、[Parallels](http://www.parallels.com/products/desktop/ "Parallels") 或 [Hyper-V](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v "Hyper-V")，不过这三个都是收费的，本篇教程将使用VirtualBox5.2.10。

### 安装 Homestead Vagrant Box
输入以下命令：
```bash
vagrant box add laravel/homestead
```
执行后会提示你选择盒子（这里我选择了virtualbox），输入序号3，回车即可。
如图：

<img width='100%' src='https://i.loli.net/2018/04/18/5ad7365451eba.png'>


下载可能较慢，太慢请挂vpn。

### 安装 Homestead
```bash
# 切换到用户文件夹
cd ~
# 克隆homestead项目 到 Homestead 文件夹
git clone https://github.com/laravel/homestead.git Homestead
```
git 项目克隆完成后，切换到 Homestead 文件夹，创建相关配置文件：
```bash
# 切换到Homestead目录
cd ~/Homestead
# 执行初始化
bash init.sh
```
走完上面步骤后 Homestead 文件夹里会出现一个 Homesstead.yaml 配置文件。这个文件可以配置 mac 与虚拟机的共享文件夹、Nginx 站点、数据库等等、虚拟机使用 cpu 数、内存等等。 现在我们的目的是先安装并运行 Laravel 就行了，先使用其预设值即可，先不进行修改。但是我们需要根据其预设值对 mac 进行一些操作。
继续往下：
```bash
# 切换到用户目录
cd ~
# 创建文件夹
mkdir -p code/laravel
```
### 编辑 /etc/hosts 文件
我们打开Homestead.yaml文件，看一看 ip 和 sites 两项：
```bash
ip: "192.168.10.10"
......
sites:
    - map: homestead.test
      to: /home/vagrant/code/public
......
```
ip 是指 Homestead 的 ip，sites 则是指定域名去对应虚拟机的文件目录。记住这两个值，相应的去 /etc/hosts 文件最后添加如以下格式内容即可。mac修改hosts文件请自行百度
```bash
192.168.10.10 	homestead.test
```
### 启动虚拟机
在mac命令行中输入：
```bash
cd ~/Homestead
```
切换到homestead项目所在到目录，然后输入：
```bash
vagrant up
```
中途可能会出现错误：
```bash
Check your Homestead.yaml file, the path to your private key does not exist.
```
解决方法：
```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
eval "$(ssh-agent -s)"
ssh-add -K ~/.ssh/id_rsa
```
### SSH 登入虚拟机
启动成功之后，输入`vagrant ssh` 登陆到 vagrant 虚拟机，如图:

<img width='100%' src='https://i.loli.net/2018/04/18/5ad73e65451dc.png'>

### 部署项目
这里以安装laravel为例：
```bash
cd code
composer create-project laravel/laravel Laravel --prefer-dist
```
安装成功后修改Homestead.yaml文件，将：
```bash
sites:
    - map: homestead.test
      to: /home/vagrant/code/public
```
修改为：
```bash
sites:
    - map: homestead.test
      to: /home/vagrant/code/laravel/public
```
然后：
```bash
cd ~/Homestead
vagrant provision
```

大功告成，浏览器输入`http://homestead.test`，出现以下页面。

<img width='100%' src='https://i.loli.net/2018/04/18/5ad7436237fb4.png'>

到这里homestead环境已经基本搭建完成了，其中更多问题作者还正在继续爬坑。。

原创文章，转载请注明出处~