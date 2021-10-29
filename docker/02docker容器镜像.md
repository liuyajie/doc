### 一、镜像命令

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



