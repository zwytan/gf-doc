# 容器部署

容器部署即使用`docker`化部署`golang`应用程序，这是在云服务时代最流行的部署方式，也是最推荐的部署方式。

## 1. 编译程序
跨平台交叉编译是`golang`的特点之一，可以非常方便地编译出我们需要的目标服务器平台的版本，而且是静态编译，非常方便地解决了运行依赖问题。
使用以下方式静态编译`Linux`平台`amd64`架构的可执行文件：
```
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o gf-app main.go
```
这样便编译出来一个`gf-app`的可执行文件。

## 2. 编译镜像
我们需要将该可执行文件`gf-app`编译生成`docker`镜像，以便于分发及部署。`Golang`的运行环境推荐使用`alpine`基础系统镜像，编译出的容器镜像约为20MB左右。

一个参考的`Dockerfile`文件如下（ 可以参考`gf-home`项目的`Dcokerfile`: https://github.com/gogf/gf-home ）：
```dockerfile
FROM loads/alpine:3.8

LABEL maintainer="john@johng.cn"

###############################################################################
#                                INSTALLATION
###############################################################################

ADD ./gf-app /bin/main
RUN chmod +x /bin/main

###############################################################################
#                                   START
###############################################################################

CMD main
```
其中，我们的基础镜像使用了`loads/alpine:3.8`这个镜像，基础镜像的`Dockerfile`地址：https://github.com/johngcn/dockerfiles ，仓库地址：https://hub.docker.com/u/loads

随后使用 `docker build gf-app .` 命令编译生成名为`gf-app`的`docker`镜像。

## 3. 运行镜像

使用以下命令运行镜像：
```
docker run gf-app
```

## 4. 镜像分发

容器的分发可以使用`docker`官方的平台：https://hub.docker.com/ ，国内也可以考虑使用阿里云：https://www.aliyun.com/product/acr 。

## 5. 容器编排

在企业级生产环境中，`docker`容器往往需要结合`kubernetes`或者`docker swarm`容器编排工具一起使用。
容器编排涉及到的内容比较多，感兴趣的同学可以参考以下资料：
* https://kubernetes.io/
* https://docs.docker.com/swarm/



