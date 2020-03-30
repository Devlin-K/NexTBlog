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

>>Client: 

* 安装node

1.node下载 官网下载，可以的话推荐使用 nvm 安装，

2.打开cmd查看node安装情况  ```node -v```，```npm -v```

3.配置全局环境 进入安装目录，创建文件夹node_global和node_cache 并执行

```npm config set prefix "D:\programming\nodejs\node_global"```

```npm config set cache "D:\programming\nodejs\node_cache"```

4.环境配置：新增环境变量NODE_PATH和添加Path，两个值都为 D:\programming\nodejs\node_global


* 安装Hexo

1.安装hexo-cli ```npm i hexo-cli -g```

随便找个地方初始化文件，执行如下命令：

npm run hexo
新建npm脚本

法 3：你还可以直接在hexo-blog中下载
npm i hexo-cli
npm run hexo （还是要在package.json中新建脚本）
初始化hexo项目
如果是按照上一节步骤 4 过来的，则在hexo-blog文件下初始化
hexo init myblog && cd myblog
npm i
下载主题
git clone https://github.com/iissnan/hexo-theme-next themes/next
在本地配置文件中设置theme属性

添加next主题

本地执行hexo项目，添加start脚本
npm start
新建npm-start脚本

打开 http://localhost:4000 验证效果吧

git环境搭建
git安装：官网下载

生成ssh认证，执行如下命令

git config --global user.name "yourname"
git config --global user.email youremail@example.com
ssh-keygen -t rsa -C "youremail@example.com"
git config --global core.autocrlf false  // 禁用自动转换，这个不设置后面上传时会出现警告，如下
git警告

最后获取到的ssh认证在C:\Users\yourname\.ssh中


>>Server:

















三、服务器配置
搭建远程Git私库
登录到远程服务器，推荐使用Xshell 5

安装 git

git --version // 如无，则安装
yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel perl-devel
yum install -y git
创建用户并配置其仓库
useradd git
passwd git // 设置密码
su git // 这步很重要，不切换用户后面会很麻烦
cd /home/git/
mkdir -p projects/blog // 项目存在的真实目录
mkdir repos && cd repos
git init --bare blog.git // 创建一个裸露的仓库
cd blog.git/hooks
vi post-receive // 创建 hook 钩子函数，输入了内容如下
#!/bin/sh
git --work-tree=/home/git/projects/blog --git-dir=/home/git/repos/blog.git checkout -f
添加完毕后修改权限

chmod +x post-receive
exit // 退出到 root 登录
chown -R git:git /home/git/repos/blog.git // 添加权限
测试git仓库是否可用，另找空白文件夹
git clone git@server_ip:/home/git/repos/blog.git
如果能把空仓库拉下来，就说明 git 仓库搭建成功了

git仓库测试

建立ssh信任关系，在本地电脑
ssh-copy-id -i C:/Users/yourname/.ssh/id_rsa.pub git@server_ip
ssh git@server_ip // 测试能否登录
注：此时的 ssh 登录 git 用户不需要密码！否则就有错，请仔细重复步骤 3-5

为了安全起见禁用 git 用户的 shell 登录权限，从而只能用 git clone，git push 等登录
cat /etc/shells // 查看 git-shell 是否在登录方式里面
which git-shell // 查看是否安装
vi /etc/shells
添加上2步显示出来的路劲，通常在 /usr/bin/git-shell
修改/etc/passwd中的权限

// 将原来的
git:x:1000:1000::/home/git:/bin/bash

// 修改为
git:x:1000:1000:,,,:/home/git:/usr/bin/git-shell
搭建nginx服务器
下载并安装nginx
cd /usr/local/src
wget http://nginx.org/download/nginx-1.15.2.tar.gz
tar xzvf nginx-1.15.2.tar.gz
cd nginx-1.15.2
./configure // 如果后面还想要配置 SSL 协议，就执行后面一句！
./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-file-aio --with-http_realip_module
make && make install
alias nginx='/usr/local/nginx/sbin/nginx' // 为 nginx 取别名，后面可直接用
配置nginx文件
先启动是否安装成功

nginx // 直接来！浏览器查看 server_ip，默认是 80 端口
修改配置文件

nginx -s stop // 先停止nginx
cd /usr/local/nginx/conf
vi nginx.conf
修改 root 解析路径，如下图
同时将 user 改为 root 如下图，不然nginx无法访问 /home/git/project/blog
nginx -s reload
修改nginx配置

修改nginx user

四、发布
至此我们就把本地和服务器的环境全部搭建完成，现在利用 hexo 配置文件进行链接

配置_config.yml文件
编辑 _config.yml 的 deploy 属性
编辑本地deploy

在 package.json 中添加 npm 脚本
"scripts": {
  "deploy": "hexo clean && hexo g -d",
  "start": "hexo clean && hexo g && hexo s"
},
链接！这下在本地调试就用npm start，调试好了就上传到服务器，美滋滋~快通过你的服务器 ip 访问吧
npm run deploy