## 拉取镜像
```
docker pull morunchang/fastdfs
```
## 运行tracker
```
docker run -d --name tracker --net=host morunchang/fastdfs sh tracker.sh
```
## 运行storage
```
docker run -d --name storage --net=host -e TRACKER_IP=<your tracker server address>:22122 -e GROUP_NAME=<group name> morunchang/fastdfs sh storage.sh
/*
  1.使用的网络模式是–net=host, <your tracker server address> 替换为你机器的Ip即可
  2.<group name> 是组名，即storage的组
  3.如果想要增加新的storage服务器，再次运行该命令，注意更换 新组名

  4. docker ps 查看容器信息 
[root@iZh3cshm0xz7wjZ ~]# docker ps 
CONTAINER ID        IMAGE                COMMAND             CREATED              STATUS              PORTS               NAMES
ccdf6bbeab48        morunchang/fastdfs   "sh storage.sh"     5 seconds ago        Up 4 seconds                            storage
a7253c93bce1        morunchang/fastdfs   "sh tracker.sh"     About a minute ago   Up About a minute                       tracker
*/
```
## 修改nginx的配置，不拦截上传内容
```
//1.进入容器内部
docker exec -it storage  /bin/bash

 // storage 是 docker ps 中的NAMES
 // exit 退出

root@iZh3cshm0xz7wjZ:/# cd data
root@iZh3cshm0xz7wjZ:/data# ls
fast_data  fastdfs  fastdfs-nginx-module  libfastcommon  nginx  nginx-1.9.11.tar.gz

//2.修改nginx配置文件
root@iZh3cshm0xz7wjZ:/# vi /data/nginx/conf/nginx.conf

//3. 添加修改内容
location /group1/M00 {
   proxy_next_upstream http_502 http_504 error timeout invalid_header;
     proxy_cache http-cache;
     proxy_cache_valid  200 304 12h;
     proxy_cache_key $uri$is_args$args;
     proxy_pass http://fdfs_group1;
     expires 30d;
 }

//4.退出
root@iZh3cshm0xz7wjZ:/data/nginx/conf# exit
exit

//5. 重启storage服务
[root@iZh3cshm0xz7wjZ ~]# docker restart storage
storage
```
