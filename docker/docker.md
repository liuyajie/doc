### 一、docker的安装

#### 1、卸载docker

```shell
yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine
```

#### 2、安装yum管理工具

```shell
yum install -y yum-utils
```

#### 3、安装一个docker下载源

```shell
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

#### 4、安装docker

```shell
yum install docker-ce docker-ce-cli containerd.io
```

#### 5、启动docker

```shell
systemctl start docker
```

#### 6、关闭docker

```shell
systemctl stop docker
```

#### 7、配置dockerhub加速器

```shell
mkdir -p /etc/docker   #新建一个目录
tee /etc/docker/daemon.json <<-'EOF' 
{
    "registry-mirrors": [
        "https://1nj0zren.mirror.aliyuncs.com",
        "https://docker.mirrors.ustc.edu.cn",
        "http://f1361db2.m.daocloud.io",
        "https://registry.docker-cn.com"
    ]
}
EOF
systemctl restart docker #重启docker
```

#### 9、官方的命令参考

```shell
https://docs.docker.com/engine/reference/commandline/pull/
```

### 二、docker常用命令

#### 1、镜像命令
```shell
docker search mysql 搜索镜像
docker search -f=stars=3000 mysql 搜索星星>=3000的mysql镜像

docker pull centos  下载一个镜像(默认最新版本)
docker pull centos:7   下载某一个版本镜像(具体下载哪个版本，去dockerhub上查看)

docker images 显示所有的本地镜像
docker images -a 显示所有的本地镜像（包括隐藏的本地镜像）
docker images -q 只显示本地镜像的ID

docker rmi hello-world  删除镜像（没有容器使用的）
docker rmi -f hello-world 删除镜像（强制删除，无论是否有使用）
docker rmi -f 镜像ID 镜像ID  删除多个镜像
docker rmi -f $(docker images -aq) 删除所有的镜像

docker rmi --help 查看帮助命令
```

#### 2、容器命令
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
docker rm -f（docker ps -aq） 强制删除所有的容器

docker stop  容器ID 		停止容器
docker start 容器ID  	开始容器
docker restart 容器ID 	重启容器
docker kill 容器ID		强制停止容器
```

#### 3、其他命令
```shell
docker logs -tf -n 10 容器ID  查看容器的日志

docker top 容器ID  查看容器中的进程ID

docker inspect 容器ID 查看容器的元数据信息
```






