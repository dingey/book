## 1、镜像获取
```
docker pull sebp/elk
```
## 2、容器运行
```
docker run -p 5601:5601 -p 9200:9200 -p 5044:5044 -v/root/elk/data:/data -it -d --name elk sebp/elk
```
## 3、验证
### 检查Kibana

Kibana http://localhost:5601/
ES服务检查 http://localhost:9200/_search?pretty

[参考]https://www.imooc.com/article/70996
