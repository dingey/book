#一、创建文件夹
```
mkdir -P /data/redis/7001/data
~~~
```

#二、部署redis

创建docker-compose.yml
```
version: '3.4'

x-image:
 &default-image
 publicisworldwide/redis-cluster
x-restart:
 &default-restart
 always
x-netmode:
 &default-netmode
 host

services:
 redis1:
  image: *default-image
  network_mode: *default-netmode
  restart: *default-restart
  volumes:
  - /data/redis/7001/data:/data
  environment:
  - REDIS_PORT=7001

 redis2:
  image: *default-image
  network_mode: *default-netmode
  restart: *default-restart
  volumes:
  - /data/redis/7002/data:/data
  environment:
  - REDIS_PORT=7002

 redis3:
  image: *default-image
  network_mode: *default-netmode
  restart: *default-restart
  volumes:
  - /data/redis/7003/data:/data
  environment:
  - REDIS_PORT=7003

 redis4:
  image: *default-image
  network_mode: *default-netmode
  restart: *default-restart
  volumes:
  - /data/redis/7004/data:/data
  environment:
  - REDIS_PORT=7004

 redis5:
  image: *default-image
  network_mode: *default-netmode
  restart: *default-restart
  volumes:
  - /data/redis/7005/data:/data
  environment:
  - REDIS_PORT=7005

 redis6:
  image: *default-image
  network_mode: *default-netmode
  restart: *default-restart
  volumes:
  - /data/redis/7006/data:/data
  environment:
  - REDIS_PORT=7006
```

#三、启动所有redis
```
docker-compose up -d
```

#四、部署cluster
```
docker run --rm -it inem0o/redis-trib create --replicas 1 106.14.175.92:7001 106.14.175.92:7002 106.14.175.92:7003 106.14.175.92:7004 106.14.175.92:7005 106.14.175.92:7006
```
#附
开放7001:7006,17001:17006端口
