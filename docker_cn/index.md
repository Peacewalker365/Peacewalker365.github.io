# Docker


# Docker

## 容器操作



```bash
docker version #版本信息
docker info #系统信息

docker images
# 可选项
-a --all #列出所有镜像
-q --quiet #只显示镜像的id，-aq常用

docker search <name>
docker search mysql
# 可选项
--filter=STARS=3000 #stars大于3000的

docker pull <name:tag>
#不写tag，默认latest
#分层下载

docker rmi -f <id>
docker rmi -f $(docker images -aq)
```

[docker tutorial](https://www.bilibili.com/video/BV1og4y1q7M4?p=11)

[advanced](https://www.bilibili.com/video/BV1kv411q7Qc/)



### 下载容器

installation omitted

设置开机启动

Go to dockerhub

find nginx

`docker pull nginx`

or

`docker pull nginx:1.20.1`

`docker pull redis:6.2.4`

### 查看容器

下载来的镜像都在本地

`docker images` 查看镜像

`docker rmi redis` 删除redis镜像，这里redis = redis:latest

`docker rmi 镜像名:版本号/镜像uid`

### 启动容器

启动nginx容器（应用容器，映射88端口）

`docker run [OPTIONS] IMAGE [COMMAND] [ARG...]`

一般docker run [OPTIONS] IMAGE就够了 因为剩下的一般有default setting

`docker run --name=my_nginx -d nginx` -d后台运行 -name设置名字 --restart=always 开机自启动

如果一开始没有设置好，我们也可以更新容器的设置项:

`docker update my_nginx --restart=always`

`docker ps -a`  查看所有容器 去掉-a查看运行中的容器

`docker rm <ID/name>` 移除不在运行的容器，加-f可强行移除运行中的容器

`docker stop <ID/name>` 停掉容器



### 端口映射

把容器端口映射到公网IP端口

update不能修改端口，所以需要先删除

`docker rm -f my_nginx`

然后重新启动：

`docker run --name=my_nginx -d --restart=always -p 88:80 nginx` 把公网88端口映射到容器80端口

如果部署在云服务上，一定要设置安全组规则使端口88能够访问！



## 修改容器内容

### 修改内部内容

`docker exec -it 341d87f8940f /bin/bash`以交互模式启动

所以我们进入了容器内的linux环境的bash终端

具体怎么修改 参照dockerhub对应页面的doc

`echo <h1> Welcome to my Nginx</h1> > index.html`

`exit`

`docker ps`



### 提交改变

`docker commit -a "author-name" -m "commit msg here" <ID> edited_nginx:v1.0`

把更改的容器提交成新的镜像edited_nginx

`docker run -d -p 88:80 edited_nginx:v1.0`



### 镜像保存

如何把镜像给到另一台机器

- 镜像传输
  - `docker save -o name.tar edited_nginx:v1.0` 镜像打包成压缩包
  - 通过ssh scp传输 或者sftp等
  - `docker load -i name.tar`读取压缩包
  - `docker images`
  - `docker run -d -p 88:80 edited_nginx:v1.0`
- 推送到远程仓库
  - 去dockerhub创建一个nginx仓库（在你的用户名下）
  - `docker tag nginx:v1.0 name/nginx:v1.0` 把镜像名字改成需要的名字（加上repo-name）
  - `docker login`
  - `docker push name/nginx:v1.0`
  - `docker logout`
  - 其他机器使用`docker pull`



### 挂载数据外部修改

`docker run -d -p 88:80 nginx`

修改时每次都要进去，繁琐

假设 nginx的页面藏在/usr/share/nginx/html

我们能不能把它跟本地的文件关联？

`docker run --name my_nginx -d -p 88:80 -v /data/html:/usr/share/nginx/html:ro nginx` ro就是只读，这个意思是在容器内部只读，在外部仍然可以改

注意这时候很可能/data/html是空的，所以容器的那些也是空的

`echo "something to show" > /data/html/index.html`



### 其他

`docker logs <id>` 查看日志，助于排查错误

`docker exec -it <id/name> /bin/bash` 交互模式 并进入控制台

` vi /etc/nginx/nginx.conf`  然后可以进入修改配置文件

当然，如果经常要修改的配置文件，可以挂载出来，外部修改

`-v`是可以挂载多个目标的，空格隔开即可

另外，我们可以将其他容器的文件拷贝出来或者拷贝进其他容器

`docker cp <id>:/etc/nginx/nginx.conf /data/conf/nginx.conf`

然后在挂载之后，内部配置同步成为外部复制好的配置



## 进阶实战

### 编写自己的应用

spring.io

### 部署redis

`docker pull redis`

我们希望把redis的 redis.conf和/data挂载出来

并且在docker run命令后面追加redis command去启用使用自定义配置文件启动

`docker --name my_redis -d -v /data/redis/redis.conf:/etc/redis/redis.conf -v /data/redis/data:/data -p 6379:6379 run redis:latest redis-server /usr/local/etc/redis/redis.conf`

如果映射到了公网，一定要给redis配置账户密码，以防编程矿机。

修改redis.conf

```
appendonly yes
requirepass password_here
```

`docker restart my_redis`



### 将应用打包成镜像

#### 以前的步骤

- 用springBoot打包成可执行jar包 (`java -jar name.jar`来执行jar进行检查)
- 把jar包上传服务器
- 服务器运行java -jar <文件名>



#### 现在

所有机器都安装docker，任何应用都是镜像，所有的机器都能运行。



#### How? --> Dockerfile

```dockerfile
FROM openjdk:8-jdk-slim
LABEL maintainer=author_name_here

COPY target/java-demo-0.0.1-SNAPSHOT.jar /app.jar

ENTRYPOINT ["java", "-jar", "/app.jar"]
```



`FROM`后面是基础环境，这里是jdk8的版本

`LABEL` 后面是写维护者的名字

`COPY`是从一个本地目录拷贝到一个容器内的目录，上面是从本地target下的一个文件拷贝到了容器中的根目录下

`ENTRYPOINT`后面是命令，每一个命令要隔开



`docker build -t java-demo:v1.0 .` 

. 指在当前目录下工作，Dockerfile里面的path都以此为根据

-t 后面是tag

如果构建文件不叫Dockerfile，则加上 -f 文件名

`docker run -d -p 8080:8080 java-demo:v1.0`

多用`docker ps`检查几遍，因为有问题的话，一般几秒之后就停了。



### 将镜像上传仓库

### 在其他机器上pull and run


