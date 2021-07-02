 #  Docker安装Mysql
 ## 

docker run --name docker-mysql -p 3307:3307 -e MYSQL_ROOT_PASSWORD=123456 -d registry.cn-hangzhou.aliyuncs.com/bzvs/mysql5.7:latest -v /Users/jixiaobo/docker/mysql:/etc/mysql -v /Users/jixiaobo/docker/mysql/logs:/var/log/mysql -v /Users/jixiaobo/docker/mysql/data:/etc/lib/mysql

 # 