---
title: Chrome误删用户，导致书签全部丢失找回的方法。
tags: 技术
date: 2017-05-12 08:01
comments: true
---

今天在使用Google Chrome的过程中，重新登录账户结果手贱误删了以前已登录的账户，百度了一下，大部分说的恢复方法是在路径：

`C:\Users\Administrator\AppData\Local\Google\Chrome\User Data\`

下找到bookmarks文件并将它改名为bookmarks.bak
但是chrome经过这么多版本的懈怠后，现在删除用户后这个文件夹已经找不到这个文件了，想恢复数据只能恢复这个文件。
最终我在百度找到了解决办法：

> 1、快捷键 Win+R 输入 Chrome 的用户数据路径：Windows XP:%USERPROFILE%\Local Settings\ApplicationData\Google\Chrome\UserData\Default\Vista/Win7/Win8:%LOCALAPPDATA%\Google\Chrome\User Data\Default\

> 2、打开Chrome的用户数据目录就会看见  Bookmarks 和 Bookmarks.bak ,当前书签 和 书签的备份文件，这时候把 Bookmarks 用txt打开。就能看到。

作者：sin
链接：https://www.zhihu.com/question/43177577/answer/141922408
来源：知乎

执行以上命令后，重启chrome就能看到以前的书签了。
