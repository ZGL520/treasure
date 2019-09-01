---
title: vps搭建
tags: vps
date: 2019-09-01
---

# vps搭建

1,购买需服务器
2,链接服务器
3,安装

    wget --no-check-certificate -O shadowsocks.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks.sh
    
    chmod +x shadowsocks.sh
    
    ./shadowsocks.sh 2>&1 | tee shadowsocks.log
    
4，查看配置

    vi /etc/shadowsocks.json
    
5，重启

    /etc/init.d/shadowsocks restart
    
6,有时ssh连接不上

    ssh-keygen -R hostname