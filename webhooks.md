---
title: webhooks搭建
tags: hexo
date: 2019/09/06
categories: blog
---

# webhook搭建

## 项目开发

在使用hexo搭建博客的过程中，我把文件和系统进行了分离，之前用了一种烧包的方法更新文章发布，就是每次更新文件仓库后还要到服务器执行发布脚本，这种方法让我非常不爽，所以接着上一次的文章再来说一下使用webhooks自动发布文章的问题，请阅读这篇前置文章 https://zgl1024.com/2019/09/01/hexo%20博客搭建/


首先开启一个项目

    mkdir webhook
    cd webhook
    npm init   //入口文件我改成了app.js
    npm install github-webhook-handler child_process --save

在项目下app.js中写如下代码，注意说明部分

```javascript
    var http = require('http')
    var createHandler = require('github-webhook-handler')
    var handler = createHandler({ path: '/', secret: 'root' })
    // 上面的 secret 保持和 GitHub 后台设置的一致

    http.createServer(function (req, res) {
        console.log('starting')
        handler(req, res, function (err) {
            res.statusCode = 404
            res.end('no such location')
        })
    }).listen(7777)

    handler.on('error', function (err) {
        console.error('Error:', err.message)
    })

    handler.on('push', function (event) {
        console.log('get a push');
        var exec = require('child_process').exec;
        exec('sh ~/blog/blog_webhook/deploy.sh', function(res){ //参数传部署文件的脚本，注意路径，我是这个路径，一定要找准了
            console.log(res)
        })
    })
```

然后是发布脚本，这里叫deploy.sh 放在项目目录下，上一篇文章哪里叫update_doc_pro.sh,脚本如下,说明看看这篇文章 https://zgl1024.com/2019/09/01/hexo%20博客搭建/ ，注意相关路径

```sh
    #！/bin/bash
    cd ~/blog/wukong/source
    rm -rf _posts
    git clone <文件仓库地址> _posts

    cd ~/blog/wukong
    hexo clean
    hexo generate
    rm -rf /var/www/hexo/*
    cp -r ~/blog/wukong/public/* /var/www/hexo/
```

就这样，把项目放到github仓库


## 项目部署

1，登录服务器，进入以下路径,部署webhooks项目

    cd ~/blog
    git clone <webhooks项目git地址> blog_webhook
    cd blog_webhood
    npm i

2，启动webhooks项目

    node app.js
    //最好用pm2,如果有使用如下启动
    pm2 start ./app.js

## nginx 反向代理配置

    vim /etc/nginx/nginx.conf //打开nginx配置文件

做如下配置，主要看红框配置，其他配置看这篇文章 https://zgl1024.com/2019/09/01/hexo%20%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA/ ssl配置看这篇 https://zgl1024.com/2019/09/02/nginx%E9%85%8D%E7%BD%AEssl%E8%AF%81%E4%B9%A6%E5%AE%9E%E7%8E%B0https%E8%AE%BF%E9%97%AE/


<img src="https://gitlab.com/ZGL520/MyImages/raw/master/pic2.png" alt="img"/>
    

这里反向代理里的地址就是我们webhooks项目在服务器启动后的访问地址以及端口

配置完成后保存退出文档编辑重启nginx

    nginx -s reload

## github 配置

到这里我们已经准备好了服务，再需要的就是去github的博客文件仓库配置webhooks了，记住这里是‘github的博客文件仓库’，千万不要搞错了

![webhooks](https://gitlab.com/ZGL520/MyImages/raw/master/pic3.png)



到这里webhooks就部署好了，可以往文件仓库push文件，理论上是可以看到博客自动更新了，不过是否真的更新这就看运气了，谁知道前面哪里会不会出现问题呢，哈哈，不过思路大概就是这么个思路，套路大概就是这个套路

this is test