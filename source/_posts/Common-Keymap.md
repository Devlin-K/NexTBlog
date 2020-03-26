---
title: Common Keymap
catalog: true
categories:
  - Memo
comments: true
keywords:
  - Keymap
  - DevlinKim
date: 2020-03-25 14:33:49
update: 2020-03-25 14:33:49
description: 常用快捷键，及命令，linux，windows，ubuntu and so on.
tags:
 - study
 - Memo
---

# Linux

> Vim编辑器中 gg 是跳至文件首行 ;  dG 清空整个文件

>还有一种方法就要退出VIM，然后使用echo >> file ，这样也能快速清空文件内容。

> df命令是linux系统以磁盘分区为单位查看文件系统，可以加上参数查看磁盘剩余空间信息
>> df -hl

# Windows

>查看占用端口 显示pid
>> netstat -ano|findstr "8080" 

>结束该进程
>> taskkill /f /t /im sqlservr.exe  或 taskkill /F /pid "4984"

# Ubuntu

> ubuntu ssh-keygen Permission denied (修改可执行权限)
>> sudo chown user1:user1 /home/user1/.ssh -R

> 查看系统中所有用户：
>> cat /etc/passwd | cut -f 1 -d:

# Mac

# Sql

# IdeaJ

# Sublime

