## 一、配置Dockfile
```
FROM openjdk:8-jdk-alpine
VOLUME /tmp
ADD target/order-service-1.0.0-SNAPSHOT.jar app.jar
RUN sh -c 'touch /app.jar'
ENV JAVA_OPTS=""
ENTRYPOINT [ "sh", "-c", "java $JAVA_OPTS -jar /app.jar" ]
EXPOSE 8080
```

## 一、配置jenkins
```
mvn install -Dmaven.test.skip=true -Dmaven.javadoc.skip=true dockerfile:build
```

## 二、部署docker应用
```
SpringBoot=webname

echo "Stop $SpringBoot ........................"
boot_id=`docker ps -a |grep webname |awk '{print $1}'`

if [ -n "$boot_id" ]; then
echo "Need stop $SpringBoot ........................"
docker stop $boot_id
docker rm $boot_id
else echo "No need stop $SpringBoot ........................"
fi

BUILD_ID=dontKillMe

docker run -e JAVA_OPTS='-Xms128m -Xmx256m -Dspring.profiles.active=prod' --network=host --name webname -d -p 8080:8080 -t springio/app
```

附POM配置
```
<properties>
  <docker.image.prefix>springio</docker.image.prefix>
</properties>

<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-surefire-plugin</artifactId>
      <configuration>
        <skip>true</skip>
      </configuration>
    </plugin>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
      <executions>
        <execution>
          <goals>
            <goal>repackage</goal>
          </goals>
        </execution>
      </executions>
    </plugin>
    <!-- tag::plugin[] -->
    <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>dockerfile-maven-plugin</artifactId>
      <version>1.4.9</version>
      <configuration>
         <repository>${docker.image.prefix}/${project.artifactId}</repository>
      </configuration>
    </plugin>
    <!-- end::plugin[] -->

    <!-- tag::unpack[] -->
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-dependency-plugin</artifactId>
      <executions>
        <execution>
          <id>unpack</id>
          <phase>package</phase>
          <goals>
            <goal>unpack</goal>
          </goals>
          <configuration>
            <artifactItems>
              <artifactItem>
                <groupId>${project.groupId}</groupId>
                 <artifactId>${project.artifactId}</artifactId>
                 <version>${project.version}</version>
                 </artifactItem>
               </artifactItems>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>

    <!--过滤resource下的文件-->
    <resources>
      <resource>
      <directory>src/main/resources</directory>
      <includes>
        <include>*.*</include>  <!--打包properties文件-->
        <include>**/*.*</include>
        <include>**/*</include>
        <include>Dockerfile</include>
       </includes>
    </resource>
  </resources>
</build>
```
