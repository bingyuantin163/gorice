
### docker介绍
Docker将应用程序与该程序的依赖，打包在一个文件里面。

image 文件生成的容器实例，本身也是一个文件，称为容器文件。
一旦容器生成，就会同时存在两个文件： image 文件和容器文件。
而且关闭容器并不会删除容器文件，只是容器停止运行而已。

### docker操作命令

列出所有image文件
```
docker images
```

下载镜像： `docker image pull [image name]`

构建镜像： `docker image build -t [username]/[repository]:[tag]`

发布镜像：`docker image push [username]/[repository]:[tag]`

给镜像打标签, 使用`tag`命令添加镜像标签，指向同一镜像文件：`docker tag ubuntu:lastest myubuntu:lastest`

运行及进入容器：见👇【docker run和docker exec使用】

查看docker中所有的容器：`docker ps -a`

杀死某个容器：见👇【删除容器的方法汇总】

暂停某个容器：`docker container stop [containID]`

查看某个容器日志：`docker logs`

### docker run和docker exec使用
运行镜像，使用`docker run`
`docker run -it -p 8000:8000 -v /src/webapp(本地绝对):/opt/webapp(镜像绝对路径)  ubuntu `

`-i` 打开标准输入接受用户的输入

`-t` 让docker分配一个伪终端(pseudo-tty)，并绑定到容器的标准输入上

`-u` 执行命令的用户

```sh
docker run -p 容器端口:宿主端口
    -v 宿主目录:容器目录
    -d 后台运行
    -e 设置环境变量
    -name 容器名称
    --rm 容器终止之后，自动删除文件
```

进入容器，查看状态，其中`i`,`t`参数说明和`docker run`一致
```
docker exec -it [container_name/container_id] /bin/bash
```

### 删除容器的方法汇总
- 方法一：
```sh
#显示所有的容器，过滤出Exited状态的容器，取出这些容器的ID，
sudo docker ps -a|grep Exited|awk '{print $1}'
#查询所有的容器，过滤出Exited状态的容器，列出容器ID，删除这些容器
sudo docker rm `docker ps -a|grep Exited|awk '{print $1}'`
```

- 方法二：
```sh
#删除所有未运行的容器（已经运行的删除不了，未运行的就一起被删除了）
sudo docker rm $(docker ps -a -q)
```

- 方法三：
```sh
#根据容器的状态，删除Exited状态的容器
sudo docker rm $(sudo docker ps -qf status=exited)
```

- 方法四：
```sh
# 删除处于stop的容器或者状态status=Exited的容器
sudo docker container prune
```
终止运行的容器文件，依然会占据硬盘空间，可以使用`docker rm [containerID]`命令删除。

### docker 如何删除none镜像
删除none的镜像，要先删除镜像中的容器。要删除镜像中的容器，必须先停止容器。
```sh
# 停止容器
docker stop $(docker ps -a | grep "Exited" | awk '{print $1 }')
# 删除容器
docker rm $(docker ps -a | grep "Exited" | awk '{print $1 }')
# 删除镜像
docker rmi $(docker images | grep "none" | awk '{print $3}')
```

### 创建镜像的三种方法

#### 基于已有的镜像创建
基于运行的容器来创建

#### 基于 Dockerfile 来创建
创建`Dockerfile`来创建

#### 基于本地模板来导入
使用导出后加载的方式创建,主要是用`docker save`和`docker load`
