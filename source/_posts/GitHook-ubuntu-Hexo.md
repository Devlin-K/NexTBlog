---
title: ubuntu下创建git钩子，同步hexo博客
catalog: true
categories:
  - study
comments: true
keywords:
  - DevlinKim
  - Githook
  - hexo
  - ubuntu
date: 2020-03-26 14:32:22
update: 2020-03-26 14:32:22
description:
tags:
  - study
  - config
  - ubuntu
  - hexo
---

#ubuntu下创建git钩子，同步hexo博客

达成目标所需环境大致有：```git```,```php```,```npm```,```hexo```,```可以用的脑子```.

>首先安装php环境
>>sudo add-apt-repository ppa:ondrej/php

>>sudo apt-get update #拉取更新源

>>sudo apt-get install php7.2-fpm

>>sudo apt-get install php7.2-mysql

开启php-mysql扩展

>>sudo vim /etc/php/7.2/fpm/php.ini

搜索 extension=pdo_mysql
把extension=pdo_firebird 与 extension=pdo_mysql 的分号去掉

>>sudo service php7.2-fpm restart

>>sudo service nginx restart