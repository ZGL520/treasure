---
title: hexo 博客搭建
tags: hexo
date: 2019/09/01
categories: blog
---

# hexo 博客搭建

## 说明

hexo搭建博客系统，按照官方推荐方法将静态md文件放在source文件夹下的_posts文件夹下，通过命令会自动生成静态文件发布，但是我不想我的博客md文件与hexo系统嵌入太深，所以我决定将系统和文件两个模块分开，分别存放在两个github仓库中，在部署时通过脚本更新发布博客，下面是具体实现思路

1，本地搭建hexo系统，具体方法后面介绍

2，本地搭建配置完成后将代码上传到github仓库，应为系统配置会涉及到一些安全敏感信息，所以该仓库为私有仓库

3，准备好博客文件仓库，博客是公开发表的文件，所以可以是公开仓库

4，准备服务器，具体实现后面介绍

5，部署hexo项目

6，通过脚本将博客文件仓库中的文件更新发布到项目中


## 实现

1，本地搭建hexo系统, 默认你已经拥有npm

    npm install -g hexo-cli // 安装 hexo-cli

    hexo init filename //初始化项目

    cd filename  //进入项目

    npm install //安装依赖

    hexo server // 启动项目，默认4000端口

    hexo server -p 80 //启动项目， 指定80端口

搭建好项目需要按照需求对项目进行配置，具体见 https://hexo.io/zh-cn/

准备更新发布博客所需脚本 update_doc_pro.sh 该文件放到项目根目录下

    #！/bin/bash
    cd ./source  //进入source文件，这里是服务器路径，不是本地路径，下面也是
    rm -rf _posts   //删除就有文件
    git clone https://github.com/ZGL520/treasure.git _posts   //拉取最新github仓库博客文件

    cd ~/blog/wukong  //进去项目根目录
    hexo clean   //清除缓存
    hexo generate   //生成静态文件
    rm -rf /var/www/hexo/*  //删除nginx为该项目配置的访问路径目录下的所有文件
    cp -r ~/blog/wukong/public/* /var/www/hexo/  //将生成的静态文件拷贝到 nginx 配置的该项目路径下，完成发布更新

如果本地开发需要更新github博客文件仓库的文件，使用如下脚本 update_doc_dev.sh, 同样是放在项目根目录下

    #！/bin/bash
    cd ./source  //进入source目录
    rm -rf _posts //删除就有文件
    git clone https://github.com/ZGL520/treasure.git _posts  //拉取最新文件


2，到这里开发配置好了系统，博客文件仓库，以及发布更新博客脚本，下面进行服务器准备，我采用的是阿里云的服务器，CentOS系统，国内的服务器解析域名需要备案，由于本人太懒，所以用了阿里的香港服务器，其实都一样，少个备案而已

    //购买服务器，登录服务器，这些就不说了,如果有文件操作权限问题自行Google一下
    yum install npm //安装npm
    npm install -g git //安装git
    npm install -g hexo-cli // 安装 hexo-cli
    yum install -g nginx //安装nginx
    cd /var
    mkdir www
    cd www
    mkdir hexo
    nginx -t //查看nginx配置地址，如果需要的话
    vim /etc/nginx/nginx.conf  //打开nginx配置文件

作如下配置

    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /var/www/hexo;
        index        index.html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }

保存配置

    nginx -s reload //nginx 重启

    //首次发布
    mkdir ~/blog //创建存放项目的文件, blog文件名，与上面脚本里blog对应
    cd ~/blog //进入该目录
    git clone <github项目仓库地址> wukong  //wukong文件名，与上面脚本里wukong对应
    cd wukong //进入项目根目录
    npm install //安装依赖
    
    sh update_doc_pro.sh //执行静态文件发布脚本

到这里就完成了，可以打开服务器的ip地址查看自己的博客，如果有解析域名的话直接打开域名，后面如果需要发布博客，只需要将写好的博客文件上传到github仓库，然后再登陆服务器进入项目目录下执行文件发布脚本（sh update_doc_pro.sh）就可以，当然这里还可以通过webhook的方式自动通知服务器更新博客文件，这里我还没有做，等做了我会补充这里的文档，或者写另一篇文档，这篇文章是我使用hexo搭建个人博客的过程记录，有一些细节可能覆盖不到。

之所以我会考虑把项目和博客文件分开，是想保持我的博客文件的独立性，我的博客文件可能需要在其他地方用到，所以我让它单独存放在一个仓库，有利于使用和管理。





