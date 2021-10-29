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

# 更新yum源索引
yum makecache fast
```

#### 4、安装docker

```shell
yum install -y docker-ce docker-ce-cli containerd.io
```

#### 5、启动docker

```shell
systemctl start docker
```

#### 6、关闭docker

```shell
systemctl stop docker #停止docker

systemctl enable docker #开启自动启动

systemctl restart docker #重启docker
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
```

#### 9、官方的命令参考

```shell
https://docs.docker.com/engine/reference/commandline/pull/
```

