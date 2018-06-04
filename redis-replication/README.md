### redis集群配置
```
1.进入到docker-compose 所在的文件夹下
2.执行命令docker-compose up -d
3.查看进程和日志 docker-compose ps && docker-compose logs
4.查看ip docker inspect redis1 | grep IP
5.测试/usr/local/bin/redis-cli -p 6379   info replication
6.链接slave查看数据 /usr/local/bin/redis-cli -p 6380  info replication
```
### 配置文件详解
```
version: '2'
services:
   redis1:
      # 指定当前构建的Docker容器的镜像
      image: docker.io/redis
      #restart: always
      command: redis-server 
      # 指定当前构建的Docker容器的名称
      container_name: redis1
      networks:
         redis_net:
            # 指定当前构建的Docker容器的IP地址
            ipv4_address: 172.22.0.2
      # 指定当前构建的Docker容器的host配置
      extra_hosts:
         - "redis1:172.22.0.2"
         - "redis2:172.22.0.3"
         - "redis3:172.22.0.4"
      # 指定当前构建的Docker容器的volume挂在目录设置
      volumes:
         - /usr/local/docker-compose/redis/redis1/config:/redis/config
         - /usr/local/docker-compose/redis/redis1/data:/redis/data
         - /usr/local/docker-compose/redis/redis1/logs:/redis/logs
      # 指定当前构建的Docker容器对外开放的端口号映射
      ports:
         - "6379:6379"
   redis2:
      image: docker.io/redis
      #restart: always
      command: redis-server --slaveof redis1 6379 # 执行对应的master地址
      container_name: redis2
      networks:
        redis_net: 
          ipv4_address: 172.22.0.3
      extra_hosts:
           - "redis1:172.22.0.2"
           - "redis2:172.22.0.3"
           - "redis3:172.22.0.4"
      volumes:#挂载文件
           - /usr/local/docker-compose/redis/redis2/config:/redis/config
           - /usr/local/docker-compose/redis/redis2/data:/redis/data
           - /usr/local/docker-compose/redis/redis2/logs:/redis/logs
        # 指定当前构建的Docker容器对外开放的端口号映射
      ports:
           - "6380:6379"
   redis3:
      image: docker.io/redis
      #restart: always
      command: redis-server --slaveof redis1 6379
      container_name: redis3
      networks:
        redis_net: 
          ipv4_address: 172.22.0.4
      extra_hosts:
           - "redis1:172.22.0.2"
           - "redis2:172.22.0.3"
           - "redis3:172.22.0.4"
      volumes:
           - /usr/local/docker-compose/redis/redis3/config:/redis/config
           - /usr/local/docker-compose/redis/redis3/data:/redis/data
           - /usr/local/docker-compose/redis/redis3/logs:/redis/logs
        # 指定当前构建的Docker容器对外开放的端口号映射
      ports:
           - "6381:6379"
networks:
  redis_net:
    driver: bridge#网络的链接方式
    ipam:
      driver: default
      config:
      - subnet: 172.22.0.0/16
        gateway: 172.22.0.1
```
