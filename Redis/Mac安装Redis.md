 - Docker 方式
 ```
 # 拉取 Redis 镜像
 docker pull redis
 # 运行 Redis 容器
 docker run --name myredis -d -p6379:6379 redis
 # 执行容器中的redis-cli，可以直接使用命令行操作 Redis
 docker exec -it myredis redis-cli
 ```
 - mac
 > brew install redis