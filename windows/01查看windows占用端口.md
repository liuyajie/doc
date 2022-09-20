```sh
# 查看占用端口  找到PID
netstat -ano | findstr "443"

# 查看哪个应用程序   使用PID
tasklist | findstr "10091"
```

