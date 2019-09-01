---
title: nginx
tags: linux
date: 2019/8/31
update: 2019/9/1
categories: linux
---

# nginx使用

## nginx 安装

    yum  install nginx

## nginx 配置

    vim /etc/nginx/nginx.conf //打开配置文件

    //添加如下配置

    server {
        listen       80 default_server; //端口号
        listen       [::]:80 default_server;
        server_name  _;
        root         /var/www/hexo; //文件跟目录
        index        index.html;  //入口文件

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


## nginx 重启

    nginx -s reload
