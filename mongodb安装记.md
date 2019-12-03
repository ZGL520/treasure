#系统准备

centos 7.5 64位

#安装

1，创建文件

    vim /etc/yum.repos.d/mongodb-org-4.2.repo
    
2,copy一下内容写入创建的文件

    [mongodb-org-4.2]
    name=MongoDB Repository
    baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.2/x86_64/
    gpgcheck=1
    enabled=1
    gpgkey=https://www.mongodb.org/static/pgp/server-4.2.asc
    
3,安装最新包

    sudo yum install -y mongodb-org
    
默认地址

    /var/lib/mongo (the data directory) data地址
    /var/log/mongodb (the log directory) log地址
    /etc/mongod.conf 配置文件地址

将访问地址与IP绑定，修改配置文件bindIp

    net:
        bindIp: 0.0.0.0

修改data or log地址配置，配置项

    storage.dbPath
    systemLog.path

参考文献

https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/