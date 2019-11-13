##  下载镜像

```
docker search nginx

docker pull nginx

docker images nginx
```

## nginx部署

```
docker run --name mynginx -d -p 80:80 \
-v /opt/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
-v /opt/nginx/conf/conf.d/:/etc/nginx/conf.d/ \
-v /opt/nginx/log:/var/log/nginx \
-v /opt/nginx/www:/opt/nginx/www \
-d docker.io/nginx 
```

## 热更新

```
docker exec -i [nginx容器名/id] nginx -s reload
```
