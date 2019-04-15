## 一、安装docker镜像

### 1、拉取镜像
```
docker pull registry.cn-hangzhou.aliyuncs.com/qida/oracle-xe-11g
```
### 2、运行镜像
```
docker run -d -p 1521:1521 --name oracle11 registry.cn-hangzhou.aliyuncs.com/qida/oracle-xe-11g
```
### 默认账号密码
```
system/oracle
```

## 二、创建数据库

### 1、在linux下的root用户切换到oracle用户
```
su - oracle
```
### 2、登录到oracle用户
```
sqlplus / as sysdba
```
### 3、创建表空间以及设置其大小
```
create tablespace TEST_DATA datafile '\u01\app\oracle\oradata\MGAK\TEST_DATA.dbf' size 100M reuse autoextend on next 40M maxsize unlimited default storage(initial 128k next 128k minextents 2 maxextents unlimited); 
```
### 4、为表空间创建用户
```
create user admin identified by admin default tablespace TEST_DATA;
```
### 5、为用户赋予权限
```
grant connect,resource to admin;
```
