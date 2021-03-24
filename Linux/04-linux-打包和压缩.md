### 一、介绍

- 打包：把几个文件或文件夹打包成一个文件，这个叫打包，大小不会有明显的变化。
- 压缩：把一个或几个文件，按算法进行压缩，大小有明显变化，方便传输。
- 格式的后缀一定要写清楚，方便使用。其实瞎写也能打包和压缩，只是不知道是哪种格式的打包或压缩文件，造成一定的障碍。

### 二、命令

#### 1、tar打包和解打包

```shell
tar cvf test.tar dir 1.txt 2.txt		  #打包文件夹和文件为一个文件

tar xvf test.tar						#解打包一个压缩文件到当前目录
tar xvf test.tar -C /tmp				 #解打包一个压缩文件到指定目录

tar tvf test.tar						#查看一个压缩文件中的内容
```

#### 2、gzip压缩和解压缩

- 只能用来压缩文件，不能用来压缩目录，最好的使用是压缩一个打包好的文件。
- 如果压缩目录的话，它把目录下的每个文件都压缩一遍

```shell
gzip 1.txt				#压缩文件，生成1.txt.gz，并删除源文件
gzip -c 1.txt > 1.txt.gz #压缩文件，生成1.txt.gz，保留源文件

gunzip 1.txt.gz			#解压文件，解压之后，删除源文件,gunzip 等于 gzip -d

gzip -r dir          	#压缩目录，把目录下的每个文件都压缩一遍，没有任何意义
gunzip -r dir			#解压目录，把目录的压缩文件都解压一遍，没有任何意义
```

#### 3、bzip2压缩和解压缩（需要安装）

- 只能压缩文件，不能压缩目录，压缩目录会报错

```shell
bzip2 1.txt			#压缩文件，生成1.txt.bz2，并删除源文件
bzip2 -k 1.txt		#压缩文件，生成1.txt.bz2，保留源文件

bunzip2 1.txt.bz2   #解压文件，解压之后，删除源文件,bunzip2 等于 bzip2 -d
bunzip2 -k 1.txt.bz2 #解压文件，解压之后，保留源文件
```

#### 4、tar打包压缩和解打包压缩

- 一步到位，直接打包压缩，解压也是如此

```shell
tar zcvf 1.txt.tar.gz 1.txt     		#打包压缩，生成1.txt.tar.gz,保留源文件
tar zxvf 1.txt.tar.gz					#解打包压缩，生成1.txt.tar.gz,保留源文件

tar jcvf 1.txt.tar.bz2 1.txt			#打包压缩，生成1.txt.tar.bz2,保留源文件
tar jxvf 1.txt.tar.bz2					#解打包压缩，生成1.txt.tar.bz2,保留源文件

#查看包里头内容也是同理 -t 即可
```

#### 5、zip打包压缩和解打包压缩（需要安装）

```shell
zip test.zip 1.txt				#将文件压缩进test.zip文件
zip -m test.zip 1.txt			#将文件压缩进test.zip文件，并删除源文件
zip -r test.zip dir				#将目录压缩进test.zip文件，不加-r，啥也压缩不进去

unzip test.zip					#将压缩文件的内容解压到当前目录
unzip -d /tmp test.zip  		#将压缩文件的内容解压到指定目录
unzip -t test.zip				#测试压缩文件是否有问题
```

