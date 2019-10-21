## 一、配置jenkins
```
mvn install -Dmaven.test.skip=true -Dmaven.javadoc.skip=true dockerfile:build
```

## 二、部署docker应用
```
SpringBoot=app

echo "Stop $SpringBoot ........................"
boot_id=`docker ps -a |grep web-admin |awk '{print $1}'`
docker stop $boot_id
docker rmi $boot_id


BUILD_ID=dontKillMe

docker run -e JAVA_OPTS=‘-Xms128m -Xmx256m -Dspring.profiles.active=prod‘ --network=host --name web-admin -d -p 8080:8080 -t springio/app
```
