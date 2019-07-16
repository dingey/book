## 一、拉取镜像

```
docker pull elasticsearch:6.8.0
```
##二、运行
```
docker run --name elasticsearch -d -e ES_JAVA_OPTS="-Xms298m -Xmx298m" -m 298M --oom-kill-disable -p 9200:9200 -p 9300:9300 elasticsearch:6.8.0
```
