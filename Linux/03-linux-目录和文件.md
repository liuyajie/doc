#### 1、cd 切换目录

```shell
cd ~ 				#切换到家目录
cd -				#切换到上次所在目录
cd /root/dev		 #切换到指定目录
```

#### 2、pwd显示当前所在目录

```shell
pwd					#显示当前所在的目录
```

#### 3、ls查看目录中的文件

```shell
ls					#显示当前目录中的文件
ls	/root			#显示指定目录中的文件（不包含隐藏文件）
ls -a /root			#显示所有文件
ls -l /root			#以长格式显示
ls -h /root			#以人类看得懂的格式显示
ls -l 等于 ll 	   #ll是ls -l的别名，两者相等
```

#### 4、mkdir创建新的目录

```shell
mkdir test				#创建新目录
mkdir -p tt/dd/ee		 #循环创建新目录
```

#### 5、rmdir删除空目录

```shell
rmdir test				#删除空目录，如果该目录下有任何东西，都会删除失败
rmdir -p tt/dd/ee		#循环删除空目录
```

#### 6、touch创建新文件

```shell
touch 11 22 33			#创建新文件
```



















