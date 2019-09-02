---
title: nginx配置ssl证书实现https访问
tags: nginx
date: 2019/9/2
update: 2019/9/2
categories: nginx
---


# nginx配置ssl证书实现https访问

1,上阿里申请一个免费的ssl证书，我是这么干的，不免费也行，其他机构也行，总之搞到一个ssl证书

2,申请通过后下载证书，选择服务类型是nginx的那个，得到两个文件，名字像下面这样

    name.pem
    name.key

3,把这俩文件上传到服务器，放到如下位置，我是放这里

    /etc/nginx/cert

4,打开nginx配置

    vim /etc/nginx/nginx.conf   //这个位置自己确定一下，我的是在这里

5，做如下配置,添加一串ssl配置，有说明的地方改一下, 

    server {
        listen       443 ssl;
        listen       80;
        #listen       [::]:80 default_server;
        server_name  _;
        root         /var/www/hexo; //项目根目录
        index        index.html; //项目入口文件
        ssl on;
        ssl_certificate /etc/nginx/cert/name.pem;  //上传那两个文件之一
        ssl_certificate_key /etc/nginx/cert/name.key; //上传那两个文件之一
        ssl_session_timeout 5m;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
        ssl_prefer_server_ciphers on;
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