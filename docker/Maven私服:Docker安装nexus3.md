## 查找nexus3镜像
```
docker search nexus3
```

## 拉取nexus3镜像
```
docker pull docker.io/sonatype/nexus3
```

##运行nexus容器
```
docker run -d -p 8081:8081 -v /root/nexus3/nexus-data:/var/nexus-data --name=nexus3 --privileged=true docker.io/sonatype/nexus3
```
解释：
* -id 创建守护式容器
* --privileged=true 授予root权限（挂载多级目录必须为true，否则容器访问宿主机权限不足）
* --name=名字 给你的容器起个名字
* -p 宿主机端口：容器端口映射
* -v 宿主机目录：容器目录 目录挂载

## 登录
默认admin密码admin123

## 在项目中配置私服
拷贝public仓库地址
![Image text](https://upload-images.jianshu.io/upload_images/10533664-ee48280eddd19722.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

配置到你本地maven的settings文件
注意：是public group仓库地址而不是releases或snapshots仓库，public默认包含了这两个仓库
```
<profiles>
    <profile>  
    <id>dev</id>  
    <repositories>  
     <repository>  
        <id>local-nexus</id>
        <url>http://192.168.3.128:8081/repository/maven-public/</url>  
        <releases>  
          <enabled>true</enabled>
        </releases>  
        <snapshots>  
          <enabled>true</enabled>  
        </snapshots>  
      </repository> 
    </repositories>  
  </profile>  
  </profiles>
```
配置maven settings文件的服务器用户名密码
注意：id为私服中releases和snapshots仓库名，必须一致
```
<servers>
    <server>  
        <id>maven-releases</id>  
        <username>admin</username>  
        <password>admin123</password>  
      </server>  
      <server>  
        <id>maven-snapshots</id>  
        <username>admin</username>  
        <password>admin123</password>  
      </server> 
  </servers>
  ```
在项目父pom文件中配置部署环境，注意id及URL必须与nexus仓库对应
```
   <!--私服仓库-->
    <distributionManagement>
        <repository>
            <id>maven-releases</id>
            <name>Nexus Release Repository</name>
            <url>http://192.168.3.128:8081/repository/maven-releases/</url>
        </repository>
        <snapshotRepository>
            <id>maven-snapshots</id>
            <name>Nexus Snapshot Repository</name>
            <url>http://192.168.3.128:8081/repository/maven-snapshots/</url>
        </snapshotRepository>
    </distributionManagement>
```
重新打开项目，对需要的模块进行deploy
![Image text](https://upload-images.jianshu.io/upload_images/10533664-9747f9b79e9dfdcd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)
在nexus中查看上传的jar
![Image text](https://upload-images.jianshu.io/upload_images/10533664-ef01707448a12e4f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)
