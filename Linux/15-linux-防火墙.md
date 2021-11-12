#### 一、启用防火墙

```shell
systemct status|start|stop|restart firewalld #查看防火墙的状态

systemct enable|disable firewalld #开机自启动
```

#### 二、防火墙的设置

```shell
firewall-cmd --state #查看状态
firewall-cmd --list-all #查看规则

firewall-cmd --list-ports #查看开放的端口
firewall-cmd --add-port=3306/tcp --permanent  #添加端口
firewall-cmd --reload #加载配置
firewall-cmd --remove-port=3306/tcp --permanent #删除端口
firewall-cmd --reload #加载配置

firewall-cmd --add-port=3300-3399/tcp --permanent  #添加范围端口
firewall-cmd --reload #加载配置
firewall-cmd --remove-port=3300-3399/tcp --permanent #删除范围端口
firewall-cmd --reload #加载配置
```

