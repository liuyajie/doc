#### 一、LOCATION

含义：匹配到谁，使用谁里边的内容处理这一次请求。

用法：有5种规则，都是用来匹配$uri的，也就是匹配这种形式`/index/document/test.html`的路径。如果内容是字符串不加引号，是正则表达式必须加引号。

```nginx
# 优先级 5 
location = /test {
    
}
# 优先级 4
location ^~ /test {
    
}
# 优先级 3
location ~ '/\d*{4}' {
    
}
# 优先级 2
location ~* '/\d*{4}' {
    
}
# 优先级 1
location /test {
    
}
```

```nginx
# 实际使用建议
# 用来兜底
location / {
    
}
# 强制处理，不受规则影响
location ^~ /static {
    
}
# 通用文件处理
location ~ \.(png|jpg|jpeg|gid)$ {
    
}
```



