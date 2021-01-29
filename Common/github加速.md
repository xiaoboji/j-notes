# GitHub加速访问
***
查询以下三个链接的DNS解析地址
1. github.com
2. assets-cdn.github.com
3. github.global.ssl.fastly.net

- [域名转IP查询](https://fastly.net.ipaddress.com/github.global.ssl.fastly.net)

写入C:\Windows\System32\drivers\etc\hosts文件中(管理员运行)

hosts一般有两个作用

1. 加快域名解析

对于要经常访问的网站，我们可以通过在 Hosts 中配置域名和 IP 的映射关系，这样当我们输入域名计算机就能很快解析出 IP ，而不用请求网络上的 DNS 服务器。

>140.82.114.4      github.com          # github 

2. 屏蔽不想要访问的地址

现在有很多网站不经过用户同意就将各种各样的插件安装到你的计算机中，有些说不定就是木马或病毒。对于这些网站我们可以利用 Hosts 把该网站的域名映射到错误的 IP 或自己计算机的 IP ，这样就不用访问了。比如不想访问 www.XXXX.com ，那我们在Hosts写上以下内容： 

> 127.0.0.1 XXXX.com #屏蔽的网站 


最后，打开命令行，刷新DNS缓存：ipconfig /flushdns
