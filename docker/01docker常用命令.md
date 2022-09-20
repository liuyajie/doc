### 零、运维命令

#### 1、查看信息

```shell
# 查看docker的版本
docker version
# 查看docker的详情
docker info
# 查看某个命令的帮助文档
docker pull --help
```

#### 2、查看镜像、容器、数据卷占用空间

```shell
docker system df
```

#### 3、查看容器输出日志

```shell
docker logs web # 查看日志

docker logs -f web # 以滚动形式查看日志

docker logs -tf web  # 以滚动形式查看日志，前边加上日期

docker logs -tf -n 10 web # 以滚动形式查看日志，前边加上日期，默认展示10条
```

### 一、镜像命令

#### 1、获取镜像

```shell
$ docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]

docker pull ubuntu:18.04 # 下载ubuntu:18.04镜像
docker pull ubuntu # 下载ubuntu:latest镜像
docker pull docker.io/library/ubuntu:18.04 # 完整地址-下载ubuntu:18.04镜像

# 不写前缀，默认是官方仓库下的镜像
# 不写标签，默认是latest
docker pull liuyajie/nginx:v1 # liuyajie下边的nginx仓库的v1版本
dokcer pull docker.io/liuyajie/nginx:v1 # liuyajie下边的nginx仓库的v1版本
```

#### 2、查看镜像

- 镜像体积，展示的可能大于实际占用的空间。因为使用的联合文件系统，很多镜像使用相同的层。
- 虚悬镜像，仓库和标签为none，因为新镜像使用旧镜像的名称导致的。
- 中间层镜像，仓库和标签为空，与虚悬镜像不同，中间层镜像是顶层镜像的依赖。

```shell
docker image ls  # 查看顶层镜像 别名 docker images

docker image ls -f dangling=true # 查看虚悬镜像
docker image prune # 清空虚悬镜像

docker image ls -a # 可以查看中间层镜像

docker image ls nginx # 只查看nginx仓库的镜像
docker image ls nginx:1.20 # 只查看nginx:1.20镜像

docker image ls -f before=nginx:1.20 # 查看nginx之前的镜像
docker image ls -f since=nginx:1.20  # 查看nginx之后的镜像

docker image ls -q # 只展示镜像的ID列
docker image ls --format "{{.ID}}"  # 只展示镜像的ID列
docker image ls --format "table {{.ID}} {{.Repository}}" # 展示ID和仓库
```

#### 3、删除镜像

```shell
docker image rm f652ca386ed1 # 用镜像ID删除镜像
docker image rm nginx # 用名称删除镜像，不写标签删除的是nginx:latest
docker image rm nginx:v1 # 用名称和标签删除镜像

docker image rm `docker image ls -q redis` # 删除所有的redis镜像
```

#### 4、利用commit理解镜像构建

​		具体的构建镜像参考Dockerfile

```shell
# 运行一个容器
docker run -d --name webserver -p 80:80 nginx
# 修改容器中文件的内容
docker exec -it webserver bash
echo '<h1>Hello,Docker</h1>' > /usr/share/nginx/html/index.html
# 把这个容器提交成镜
docker commit --author 'liuyajie <920288326@qq.com>' --message '修改默认网页' webserver nginx:v1
# 运行新生成的镜像
docker run -d --name webserver -p 81:80 nginx:v1
```

```shell
# 保存镜像
docker save lovenginx:1.11 -o lovenginx.tar

# 导入镜像
docker load -i lovenginx.tar
```

#### 5、Dockerfile镜像构建方式

0 构建流程

- 指定基础镜像
- RUN时，会启动容器执行命令
- 把该容器打包成镜像（回顾commit的构建方式）
- 镜像上下文（重点理解）

1 初尝构建

```shell
mkdir mynginx
cd mynginx
touch Dockerfile
vim Dockerfile

FROM nginx:latest
RUN echo '<h1>Hello,World.Hello,Docker</h1>' > /usr/share/nginx/html/index.html

docker build -t nginx:v2 .
```

2 FROM指定基础镜像

3 RUN执行命令

- RUN shell命令
- RUN ["命令文件"，"参数1"，"参数2"]
- 注意：尽量写一个命令完成所有的事情，最后也要打扫干净，否则会生成臃肿的镜像。

4 镜像上下文

​	-t 指定镜像名称和标签

​	-f 指定dockerfile，不写则默认是Dockerfile

​	. 上下文，由于是B/S架构，会把你指定的上下文发送给服务器，所以写添加、复制文件时要注意，你需要的文件是否存在于上下文中

5 可以从不同的地方构建镜像

```shell
# git上构建，那里边也有上下文
docker build -t hello-world https://github.com/docker-library/hello-world.git#master:amd64/hello-world	

# 压缩包构建，包里头也有上下文
docker build http://server/context.tar.gz

# 标准输入构建，没有上下文
cat Dockerfile | docker build -

# 标准输入压缩包构建，包里头有上下文

```

### 二、容器命令

#### 1、启动容器

- 检查本地是否存在指定的镜像，不存在就从 registry 下载
- 利用镜像创建并启动一个容器
- 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
- 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
- 从地址池配置一个 ip 地址给容器
- 执行用户指定的应用程序
- 执行完毕后容器被终止

```shell
docker run ubuntu  /bin/echo 'hello world' # 如果镜像 ubuntu:latest 存在则运行，否则拉取一个镜像并运行
docker run ubuntu:18.04 /bin/echo 'hello world' # 如果镜像 ubuntu:18.04 存在则运行，否则拉取一个镜像并运行

docker run -it ubuntu /bin/bash # 交互模式启动容器 exit退出容器
docker run -it --name nice_swanson ubuntu /bin/bash # 启动容器起一个名字

docker start nice_swanson # 启动一个停止的容器 别名 docker container start
docker restart nice_swanson # 重启一个停止或运行中的容器 别名 docker container restart
```

​	后台运行，容器启动之后是否一直在运行，不取决于-d参数，取决于容器内是否有前台程序在运行

```shell
# 容器内有前台程序一直在运行，会一直在打印东西
docker run ubuntu /bin/sh -c "while true;do echo hello world;sleep 1;done;"

# 这种程序加上参数，也没有问题的
docker run -d ubuntu /bin/sh -c "while true;do echo hello world;sleep 1;done;"
```

#### 2、终止容器

```shell
docker stop nginx # 停止容器 别名 docker container stop
docker kill nginx # 强制停止容器 别名 docker container kill
```

#### 3、进入容器

```shell
docker attach nginx # 进入正在运行的容器，如果退出，容器也会退出的     不推荐

docker exec -it nginx /bin/bash # 进入正在运行的容器，如果退出，容器不受影响 推荐
```

#### 4、删除容器

```shell
docker rm nginx # 删除容器（已停止） 别名 docker container rm 

docker rm -f $(docker ps -aq) # 强制删除所有的容器（运行中+已停止）
docker container prune -f # 删除所有已停止的容器
```

#### 5、导出和导入容器（快照）

```shell
# 导出（运行中、已停止的容器都可以）
docker export 7691a814370e > ubuntu.tar

# 导入成镜像，重名和重标签的话会覆盖已有镜像，已有镜像编程虚悬镜像
cat ubuntu.tar | docker import - test/ubuntu:v1.0
docker import ubuntu.tar test/ubuntu:v1.0
docker import ubuntu.tar ubuntu:v1.0

docker import http://example.com/exampleimage.tgz example/imagerepo
```

#### 6、运维容器

```shell
docker ps  nginx # 查看运行中的容器 别名 docker container ls
docker ps -a nginx # 查看运行中+停止的容器
docker ps -aq nginx # 查看所有运行容器的ID

docker top nginx  # 查看容器中的进程

docker inspect nginx # 查看容器的详情信息
```

#### 7、拷贝

```shell
# 从容器内往外部拷贝文件
docker ps aa:/usr/share/nginx/html .
```

### 三、数据管理

#### 1、数据卷，挂载具名卷

新增，数据卷

> docker volume create my-vol

查看，系统所有的数据卷列表

> docker volume ls

查看，数据卷的详情，会在`/var/lib/docker/volumes/my-vol/_data`创建一个空目录

> docker volume inspect my-vol

查看，容器使用数据卷的详情，在Mounts里边有挂载的数据卷详情，第二条命令是只看挂载的数据卷信息

> docker inspect ubuntu

> docker inspect ubuntu | awk '/"GraphDriver"/,/"Config": {/{if(i>1)print x;x=$0;i++}' | awk '/"Mounts"/,/: {/{if(i>1)print x;x=$0;i++}'

使用，同一个数据卷可以绑定同一个容器中的多个目录，那么这些目录彼此互通

> docker run -it --name ubuntu    --mount source=my-vol,target=/root    --mount source=my-vol,target=/home ubuntu

删除，删除无主的数据卷，没有容器（运行中+已停止）引用的

> docker volume prune -f  

删除，容器时也可以删除无主的数据卷

> docker containter rm -v ubuntu

#### 2、挂载主机目录

挂载

​	-v 也能用，如果宿主目录不存在会创建一个

​	--mount 指定的比较详细，如果宿主目录不存在则报错

> docker run --name nginx -d --mount type=bind,source=/data/webapp,target=/usr/share/nginx/html nginx:alpine
>
> docker run --name nginx -d -v /data/webapp:/usr/share/nginx/html nginx:alpine

挂载只读模式，只有这个绑定的目录是只读模式，其他的目录可以修改

​	-v /data/webapp:/usr/share/nginx/html,ro

​	--mount type=bind,source=/data/webapp,target=/usr/share/nginx/html,readonly

挂载只读模式（全局），此时有可能会报错，注意指定好可读写的目录

​	--readonly

> docker run -d --name nginx --readonly --mount type=bind,source=/data/webapp,target=/usr/share/nginx/html nginx:alpine

挂载也可以只挂载一个文件

>docker run -d --name nginx -readonly --mount type=bind,source=$HOME/.bash_history,target=/root/.bash_history nginx:alpine

查看挂载详情

> docker inspect nginx

> docker inspect nginx| awk '/"GraphDriver"/,/"Config": {/{if(i>1)print x;x=$0;i++}' | awk '/"Mounts"/,/: {/{if(i>1)print x;x=$0;i++}'

####　3、挂载匿名卷

```shell
# 匿名挂载（匿名卷），就是把容器中目录出来，与具名卷差不多，都是映射会在`/var/lib/docker/volumes/`创建一个随机空目录
# Dockerfile中的volume指令也是挂载一个匿名卷
docker run -d -p 6379:6379 --name mycentos -v /src/volume01

# 具名挂载（命名卷） -v 宿主机数据卷所在路径：容器数据卷所在路径
docker run -d -p 6379:6379 --name mycentos -v /home/docker_volume:/src/volume01
```

### 四、网络

#### 1、外部访问容器

-p 指定容器端口绑定宿主机端口

```shell
docker run -d --name nginx -p 80:80 nginx:alpine # 指定80->80

docker run -d --name nginx -p 127.0.0.1:80:80 nginx:alpine # 指定127.0.0.1:80 才可以访问，外部访问不了

docker run -d --name nginx -p 127.0.0.1::80 nginx:alpine # 指定127.0.0.1:xxxx 才可以访问，外部访问不了

docker run -d --name nginx -p 80:80/tcp nginx:alpine # 指定80->80，并且是tcp连接才可以
```

-P 指定容器暴露的端口绑定宿主机端口

```
docker run -d -P --name nginx nginx:alpine
```

查看端口情况

```shell
docker port nginx
```

#### 2、容器互联

1 新建网络

```shell
# 新建一个桥接模式的网络
docker network create -d bridge my-net

# 新建一个桥接模式的网络，不写-d 默认就是桥接模式
docker network create my-net
```

2 查看网络

```shell
# 列出所有的网络
docker network ls
# 查看某个网络的详情
docker network inspect my-net
```

3 删除网络，如果网络删除了，停止的容器无法启动，哪怕新建了同名的网络也不能启动

```
# 删除网络，没有容器（运行中）引用
docker network rm my-net

# 删除所有没有容器（运行中）引用的网络
docker network prune -f 
```

4 容器互联

```shell
# 启动容器时指定链接到某个网络，两个容器都链接到同一网络，则可以互相访问
docker run --rm -it --name busybox1 --network my-net busybox sh

docker run --rm -it --name busybox2 --network my-net busybox sh
```

5 指定运行中的容器链接到某一个网络

```
docker network connect my-net busybox3
```

6 指定运行中的容器断开某一个网络

```shell
docker network disconnect my-net busybox3
```

7 查看容器的网络情况

```
docker inspect busybox3 | grep -n -A 40 "Networks"
```

#### 3、配置DNS

### 五、常用软件服务使用

```shell
mysql
docker run -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -d --name mysql01 --restart=always -v /root/own/datadir:/var/lib/mysql mysql:5.6
```



### 六、Dockerfile

#### 1、FROM

指定基础镜像，你要构建的镜像以此为基础进行构建。

```
FROM busybox
```

#### 2、RUN

如果执行创建文件的命令，默认的工作目录是根目录(/)，如果改变目录，可以cd，但这个cd只对当前行的命令有效，对下一行的RUN无效，如果想一直有效可以使用WORKDIR。

```
FROM ubuntu:latest
RUN sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-* \
&& sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-* \
&& yum install -y vim
```

#### 3、EXPOSE

给Dockerfile声明一下该服务需要的端口号，并无其他意义

```dockerfile
FROM centos:latest
EXPOSE 80 
```

#### 4、COPY

如果执行创建文件的命令，默认的工作目录是根目录(/)，可以指定工作目录

```
COPY ./nginx.conf /usr/nginx/conf.d/

COPY ./nginx* /usr/nginx/conf.d/
COPY ./ngin*.conf /usr/nginx/conf.d

COPY ./nginx/ /usr/nginx/conf.d/

COPY --chown=nginx:nginx ./nginx/ /usr/nginx/conf.d/
```

#### 5、ADD

​	功能与`COPY`一样，只是多了一些额外的功能，可以解压缩，可以是url链接，但它可以使用构建缓存失效导致构建过程十分缓慢。因此在 `COPY` 和 `ADD` 指令中选择的时候，可以遵循这样的原则，所有的文件复制均使用 `COPY` 指令，仅在需要自动解压缩的场合使用 `ADD`。

```dockerfile

```

#### 6、CMD

​	之前介绍容器的时候曾经说过，Docker 不是虚拟机，容器就是进程。既然是进程，那么在启动容器的时候，需要指定所运行的程序及参数。`CMD` 指令就是用于指定默认的容器主进程的启动命令的。

​	在运行时可以指定新的命令来替代镜像设置中的这个默认命令，比如，`ubuntu` 镜像默认的 `CMD` 是 `/bin/bash`，如果我们直接 `docker run -it ubuntu` 的话，会直接进入 `bash`。我们也可以在运行时指定运行别的命令，如 `docker run -it ubuntu cat /etc/os-release`。这就是用 `cat /etc/os-release` 命令替换了默认的 `/bin/bash` 命令了，输出了系统版本信息。

在指令格式上，一般推荐使用 `exec` 格式，这类格式在解析时会被解析为 JSON 数组，因此一定要使用双引号 `"`，而不要使用单引号。

​	提到 `CMD` 就不得不提容器中应用在前台执行和后台执行的问题。这是初学者常出现的一个混淆。Docker 不是虚拟机，容器中的应用都应该以前台执行，而不是像虚拟机、物理机里面那样，用 `systemd` 去启动后台服务，容器内没有后台服务的概念。

​	而使用 `service nginx start` 命令，则是希望 upstart 来以后台守护进程形式启动 `nginx` 服务。而刚才说了 `CMD service nginx start` 会被理解为 `CMD [ "sh", "-c", "service nginx start"]`，因此主进程实际上是 `sh`。那么当 `service nginx start` 命令结束后，`sh` 也就结束了，`sh` 作为主进程退出了，自然就会令容器退出。

```shell
# 多个CMD只有最后一个生效
CMD echo $HOME
CMD ["sh","-c","echo $HOME"]

CMD service nginx start
CMD ["sh","-c","service nginx start"]

CMD nginx -g daemon off # 不会运行的，它会解释成sh -c nginx -g daemon off
CMD ["nginx","-g","daemon off;"] # 正确运行
```

#### 5、ENTRYPOINT

​	最后执行，功能与CMD差不多，多个也只是运行最后一个。

```dockerfile
# 结合CMD来使用，这样CMD用来接收参数
FROM nginx
ENTRYPOINT ["nginx", "-c"] # 定参
CMD ["/etc/nginx/nginx.conf"] # 变参
# run运行容器时不传参
docker run  nginx:test
nginx -c /etc/nginx/nginx.conf # 最终运行
# run运行容器时传参
docker run  nginx:test -c /etc/nginx/new.conf
nginx -c /etc/nginx/new.conf # 最终运行
```

```dockerfile
# 结合脚本来使用
FROM alpine:3.4
RUN addgroup -S redis && adduser -S -G redis redis
ENTRYPOINT ["docker-entrypoint.sh"]
EXPOSE 6379
CMD [ "redis-server" ]

# docker-entrypoint.sh脚本
#!/bin/sh
# allow the container to be started with `--user`
if [ "$1" = 'redis-server' -a "$(id -u)" = '0' ]; then
    find . \! -user redis -exec chown redis '{}' +
    exec gosu redis "$0" "$@"
fi
exec "$@"
```

```dockerfile
# 二者的区别
CMD                   # 指定这个容器启动的时候要运行的命令，不可以追加命令
ENTRYPOINT            # 指定这个容器启动的时候要运行的命令，可以追加命令
```

#### 6、ENV

设置环境变量，定义了环境变量，那么在后续的指令中，就可以使用这个环境变量。构建好的容器中也会有这个环境变量。

```dockerfile
ENV NODE_VERSION 7.2.0

RUN curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.xz" \
  && curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc"
```

#### 7、ARG

构建参数，与 ENV 作用一致。不过作用域不一样。ARG 设置的环境变量仅对 Dockerfile 内有效，也就是说只有 docker build 的过程中有效，构建好的镜像内不存在此环境变量。

构建命令 docker build 中可以用 --build-arg <参数名>=<值> 来覆盖。

格式：

```dockerfile
ARG NODE_VERSION=7.2.0
```

#### 8、VOLUME

定义匿名数据卷。在启动容器时忘记挂载数据卷，会自动挂载到匿名卷。

作用：

- 避免重要的数据，因容器重启而丢失，这是非常致命的。
- 避免容器不断变大。

格式：

```
VOLUME ["/etc/nginx", "/etc/nginx"]
VOLUME /etc/nginx
```

在启动容器 docker run 的时候，我们可以通过 -v 参数修改挂载点。

### 七、Compose























