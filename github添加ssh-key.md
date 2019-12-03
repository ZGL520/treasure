# 1,创建ssh key
    
    ssh-keygen -t rsa -C <email>
    
秘钥创建后文件位置一般位于当前位子，默认名公钥：id_rsa.pub 秘钥：id_rsa
    
# 2,复制公钥

    cat id_rsa.pub
    
# 3,添加到github

打开GitHub -> 右上角头像 -> setting -> 侧边栏SSH and GPG keys -> New SSh key -> 填入title和key -> 搞定

    
这时讲道理你应该失败

# 4,我懂ssh代理 /root/.ssh/目录下

    eval `ssh-agent -s`
    ssh-add <秘钥>  //ssh-add ./is_rsa

# 5,clone 个仓库试试