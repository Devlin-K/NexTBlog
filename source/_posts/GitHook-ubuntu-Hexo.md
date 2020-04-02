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

达成目标所需环境大致有：```git```,```npm```,```hexo```,```可以用的脑子```.
不需要的有:```php```,```java```,```无糖可乐```.

>Client: 

* 安装node

1.node下载 官网下载，可以的话推荐使用 nvm 安装，

2.打开cmd查看node安装情况  ```node -v```，```npm -v```

3.配置全局环境 进入安装目录，创建文件夹node_global和node_cache 

并执行```npm config set prefix "D:\programming\nodejs\node_global"```,```npm config set cache "D:\programming\nodejs\node_cache"```

4.环境配置：
新增环境变量NODE_PATH和添加Path，两个值都为 ```D:\programming\nodejs\node_global```


* 安装Hexo

1.安装hexo-cli ```npm i hexo-cli -g```

2.随便找个地方初始化文件，执行如下命令：```npm run hexo```，```npm i hexo-cli```，```npm run hexo``` （还是要在package.json中新建脚本）

* 初始化hexo项目

1.如果是按照上一节步骤 4 过来的，则在hexo-blog文件下初始化 ```hexo init myblog && cd myblog```

2.下载主题```git clone https://github.com/iissnan/hexo-theme-next themes/next```

3.本地执行hexo项目，添加start脚本```npm start```，具体脚本自行百度，可以不用，不重要的

* git环境搭建

1.git安装：官网下载，安装，配置环境，测试，注册表修改gitbash名称为Mjölnir，并修改图标

2.生成ssh认证：
执行如下命令：```git config --global user.name "yourname"```，```git config --global user.email youremail@example.com```，```ssh-keygen -t rsa -C "youremail@example.com"```，```git config --global core.autocrlf false```

禁用自动转换，这个不设置后面上传时会出现警告

3.获取的私钥公钥保存好，有兴趣的话可以配置一对多。

>Server:

* 安装 git

1.```git --version``` 如无，
则安装```yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel perl-devel```，```yum install -y git```，

2.创建用户并配置其仓库：（中等重要，不添加用户有安全问题）
添加用户```useradd git```，```passwd git```， // 设置密码

这步很重要，不切换用户后面会很麻烦 ```su git```，

项目存在的真实目录 ```cd /home/git/```，```mkdir -p projects/blog``` 

创建一个裸露的仓库 ```mkdir repos && cd repos```，```git init --bare blog.git``` 

创建 hook ```cd blog.git/hooks```，```vi post-receive```
钩子函数，输入了内容如下```#!/bin/sh
git --work-tree=/home/git/projects/blog --git-dir=/home/git/repos/blog.git checkout -f```

3.添加完毕后修改权限（重要，不然搞不了）

退出到 root 登录 ```chmod +x post-receive```，```exit``` 

添加权限  ```chown -R git:git /home/git/repos/blog.git```

4.测试git仓库是否可用，另找空白文件夹
如果能把空仓库拉下来，就说明 git 仓库搭建成功了 ```git clone git@server_ip:/home/git/repos/blog.git```


* git仓库测试

1.建立ssh信任关系，在本地电脑```ssh-copy-id -i C:/Users/yourname/.ssh/id_rsa.pub git@server_ip```,

测试能否登录此时的 ssh 登录 git ```ssh git@server_ip```用户不需要密码！否则就有错，请仔细重复步骤 3-5

2.安全起见禁用 git 用户的 shell 登录权限，从而只能用 git clone，git push 等登录

查看 git-shell 是否在登录方式里面 ```cat /etc/shells```

查看是否安装  ```which git-shell```

添加上2步显示出来的路劲，通常在 /usr/bin/git-shell ```vi /etc/shells```

3.修改/etc/passwd中的权限

将原来的```git:x:1000:1000::/home/git:/bin/bash```

修改为```git:x:1000:1000:,,,:/home/git:/usr/bin/git-shell```

* 搭建nginx服务器

1.下载并安装nginx

首先```cd /usr/local/src```，```wget http://nginx.org/download/nginx-1.15.2.tar.gz```下载安装包

解压```tar xzvf nginx-1.15.2.tar.gz```（如果是zip包 unzip -d 位置 文件 就可以了）

然后 ```cd nginx-1.15.2```

如果后面还想要配置 SSL 协议，就执行后面一句！ ```./configure```，```./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-file-aio --with-http_realip_module```

安装 ```make && make install```

为 nginx 取别名，后面可直接用```alias nginx='/usr/local/nginx/sbin/nginx' ``` 

* 配置nginx文件

1.先启动是否安装成功

绿的就ok了，不放心在浏览器输ip试试有没有页面，熟悉的welcome nginx！```service nginx status```

2.修改配置文件

先停止nginx ```nginx -s stop``` 

~~```cd /usr/local/nginx/conf```，```vi nginx.conf```~~

可以修改```/nginx/site-available/default```,将其中root后路径改为博客部署绝对路径

然后~~```nginx -s reload```~~，或者```service nginx restart```


* 发布

至此我们就把本地和服务器的环境全部搭建完成，现在利用 hexo 配置文件进行链接

~~配置_config.yml文件,编辑_config.yml 的 deploy 属性~~

~~在 package.json 中添加 npm 脚本
"scripts": {
  "deploy": "hexo clean && hexo g -d",
  "start": "hexo clean && hexo g && hexo s"
},~~

或者直接```hexo clean```，```hexo d -g```。完事