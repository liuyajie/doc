### 一、配置GitHub加速访问

#### 1、获取GitHub官方CDN地址

```shell
https://www.ipaddress.com/
```

获取以下网址对应的IP地址

- assets-cdn.github.com
- github.com
- github.global.ssl.fastly.net

#### 2、设置host

```shell
cd C:\Windows\System32\drivers\etc
vim hosts

140.82.113.4 github.com
185.199.111.153 assets-cdn.github.com
185.199.108.153 assets-cdn.github.com
185.199.109.153 assets-cdn.github.com
185.199.110.153 assets-cdn.github.com
199.232.69.194 github.global.ssl.fastly.net
```

#### 3、刷新dns

```shell
ipconfig /flushdns
```



