### 一、容器命令

#### 0、帮助命令

```shell
docker version  查看docker版本
docker info 查看docker的系统信息，包括镜像和容器的数量
docker run --help  查看某个命令的具体用法
```

#### 1、容器命令

```shell
docker run 镜像ID  如果镜像存在则运行，否则拉取一个镜像并运行
	-it     交互式运行，进入容器内部
	-d     后台运行
	-p     指定端口 8080:80（与外部通信），80（不与外部通信），192.168.56.110:80:80（绑定IP式指定）
	-name 给运行的容器起一个名字
	
docker ps  查看运行中的容器
docker ps -a 查看运行容器的历史记录
docker ps -aq 查看所有运行容器的ID

docker rm 容器ID 删除容器（已停止）
docker rm -f $(docker ps -aq) 强制删除所有的容器

docker stop  容器ID/容器名称 		停止容器
docker start 容器ID/容器名称 		开始容器
docker restart 容器ID/容器名称 	重启容器
docker kill 容器ID/容器名称	    强制停止容器
```

#### 3、其他命令

```shell
docker logs -tf -n 10 容器ID  查看容器的日志

docker top 容器ID  查看容器中的进程ID

docker inspect 容器ID 查看容器的元数据信息
```



