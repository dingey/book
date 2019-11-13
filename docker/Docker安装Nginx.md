##  下载镜像

```
docker search nginx

docker pull nginx

docker images nginx
```

## nginx部署

```
docker run --name mynginx -d -p 80:80 -v /conf/nginx.conf:/etc/nginx/nginx.conf -v /logs/nginx:/var/log/nginx -d docker.io/nginx 
```
