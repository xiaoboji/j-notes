- dir：显示文件目录和文件
- cls：清屏
- ipconfig：显示TCP/IP、DNS等网络配置
- shutdown 允许您关闭或重新启动本地或远程计算机。如果没有使用参数，shutdown 将注销当前用户。
    * m ComputerName 指定要关闭的计算机。
    * t xx 将用于系统关闭的定时器设置为 xx 秒。默认值是 20 秒。
    * l 注销当前用户，这是默认设置。-m ComputerName 优先。
    * s 关闭本地计算机。
    * r 关闭之后重新启动 。
    * a 中止关闭。除了 -l 和 ComputerName 外，系统将忽略其它参数。在超时期间，您只可以使用 -a。
- netstat：查看某个端口号是否占用，为哪个进程所占用
    * netstat -ano
    * netstat -ano | findstr "8080"    
- tasklist: 列出所有的进程，pid 名称等
