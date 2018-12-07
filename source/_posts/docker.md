---
title: docker 命令速记
categories: 
- 笔记速记
tags: 
- docker  
---

### Docker for Beginners
- 安装
- 在Alpine Linux容器中运行单个任务`docker container run alpine hostname`
- 列出所有容器`docker container ls --all`
- 列出正在运行的容器 `docker container ls`
- 运行Docker容器并访问其shell`docker container run --interactive --tty --rm ubuntu bash`
  - --interactive 说你想要一个互动会话。
  - --tty 分配一个伪tty。
  - --rm 告诉Docker继续操作并在完成执行时移除容器。
- 键入exit退出shell会话。这将终止bash进程，导致容器退出`exit`
- 运行新的MySQL容器`docker container run  --detach --name mydb -e MYSQL_ROOT_PASSWORD=my-secret-pw  mysql:latest`
- 显示了MySQL Docker容器中的日志`docker container logs mydb`
- 容器内运行的进程`docker container top mydb` 
- 连接到已经运行的容器中的新shell进程 `docker exec -it mydb sh`
- 使用Dockerfile中的说明创建新的Docker镜像`docker image build --tag $DOCKERID/linux_tweet_app:1.0 .`
- 从镜像启动一个新的容器`docker container run --detach --publish 80:80 --name linux_tweet_app $DOCKERID/linux_tweet_app:1.0`
- 优雅地停止容器 `docker container stop`
- 启动容器`docker start dockker_name`
- 删除 `docker container rm`
- 强制删除 `docker container rm --force linux_tweet_app `
-- 查看镜像`docker image ls`
- 挂载目录到容器 `docker container run --detach --publish 80:80 --name linux_tweet_app --mount type=bind,source="$(pwd)",target=/usr/share/nginx/html  $DOCKERID/linux_tweet_app:1.0 `
- `docker container run --detach --publish 27017:27017 --name heuristic_shtern --mount type=bind,source="c:/data/db/",target=/data/db/  mongo`
- 搜索Docker主机上的镜像`docker image ls -f reference="$DOCKERID/*"`
- 在推送图像之前，您需要登录Docker Hub`docker login`提供Docker ID凭据后推送`docker image push $DOCKERID/linux_tweet_app:1.0 `,浏览`https://hub.docker.com/r/<your docker id>/`
- docker镜像检索`docker search 镜像名字`
- 删除指定镜像`docker rmi image-id`
- 挂载数据文件`docker run -p 27017:27017 -i -v /c/data/db:/data/db -d mongo`
- 挂载卷`docker container run -v HOST_PATH:CONTAINER_PATH [OPTIONS] IMAGE [CMD]`
- 创建volume`docker volume create --name html`
- 挂载volune`docker container run --name www -d -p 8080:80 -v html:/usr/share/nginx/html nginx`
- 列出网络`brctl show`
- 查找网络`docker network inspect bridge`
- 测试网络`ping -c5 172.17.0.2`
- 拉取镜像`docker image pull alpine`
- 启动交互式容器`docker container run -it alpine /bin/sh`
- 运行容器后创建镜像`docker container commit CONTAINER_ID`
- 标记镜像`docker image tag <IMAGE_ID> ourfiglet`