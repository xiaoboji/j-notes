 - Github/GitLab配置SSH
    * 打开本地git bash,使用如下命令生成ssh公钥和私钥对
    > ssh-keygen -t rsa -C 'xxx@xxx.com' 然后一路回车(-C 参数是你的邮箱地址)
    * 然后打开~/.ssh/id_rsa.pub文件(~表示用户目录，比如我的windows就是C:\Users\Administrator)，复制其中的内容
    * GitHub/GitLab上配置SSH
