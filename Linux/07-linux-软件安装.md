### 第一节、Linux软件包介绍

1. Linux软件包分为两种：源码包和二进制包，Linux下默认使用二进制包。
2. 源码包，就是一堆源码，你可以自己编译，安装，然后使用。
3. 二进制包，就是编译好的软件，可以直接安装使用。
4. 二进制包有两种管理方式，RPM管理方式和DPKG管理方式。centos使用的是RPM，ubuntu使用DPKG。

### 第二节、二进制包的安装

#### 1、RPM包的简介

命名规则：一般的二进制包长这样：`httpd-2.2.15-15.el6.centos.1.i686.rpm` 这个是包全名。

- httpd 包名、简称
- 2.2.15 包的版本号
- 15 包发行次数
- el6 发行公司
- centos 适用系统
- i686 适用的硬件平台。其他还有x86_64（64位CPU）、i386、i686、noarh（无限制）。i686的软件最多。
- 1 暂时还没有啥意义。
- rpm 扩展名，表示是编译好的二进制rpm包。

#### 2、RPM包的查询（重点）

```shell
rpm -q vim-enhanced   #查询 指定包 是否安装
rpm -qa | grep vim	  #查询 所有包 并查询vim是否安装(很常用)

rpm -qi vim-enhanced  # 查询 指定包 的详情,已安装
rpm -qip /media/cdrom/Packages/yum-utils-1.1.31-54.el7_8.noarch.rpm # 查询 指定包 的详情,未安装

rpm -qf /etc/vimrc   # 查询 某个文件属于哪个包，用来抽取文件、查看文件是否被修改，已安装
# 没有安装的情况下，查询某个文件属于哪个包，第一种方式是去网站查
# https://pkgs.org 
# http://www.rpmfind.net/
# 第二种方式是使用yum去查
# yum provides /etc/mime.types
# yum provides */mime.types
# 第三种方式是，谁有这个文件，去他的Linux上使用命令查

rpm -ql vim-enhanced #查询 该软件需要安装哪些文件，已安装
	--dump			#可以打印更为详细的信息
rpm -qlp /media/cdrom/Packages/yum-utils-1.1.31-54.el7_8.noarch.rpm # 查询 该软件需要安装哪些文件,未安装

rpm -qR vim-enhanced #查询 该软件需要依赖哪些包哪些文件，已安装
rpm -qRp /media/cdrom/Packages/yum-utils-1.1.31-54.el7_8.noarch.rpm #查询 该软件需要依赖哪些包哪些文件，未安装

```

#### 3、RPM包的安装、升级、卸载（重点）

​											RPM 包默认安装路径

| 安装路径        | 含 义                      |
| --------------- | -------------------------- |
| /etc/           | 配置文件安装目录           |
| /usr/bin/       | 可执行的命令安装目录       |
| /usr/lib/       | 程序所使用的函数库保存位置 |
| /usr/share/doc/ | 基本的软件使用手册保存位置 |
| /usr/share/man/ | 帮助文件保存位置           |

```shell
rpm -ivh /media/cdrom/Packages/httpd-2.4.6-95.el7.centos.x86_64.rpm   #正常安装，如果需要依赖其他软件，会给出提示
rpm -ivh --replacefiles  #覆盖安装，如果已经存在，就给你覆盖
rpm -ivh --force  #暴力安装，不检测依赖性，不检测是否已经安装
rpm -ivh --test   #测试安装，检测依赖性

rpm -Uvh /media/cdrom/Packages/httpd-2.4.6-95.el7.centos.x86_64.rpm #升级版本，如果不存在则安装
rpm -Fvh #升级版本，如果不存在则不安装

rpm -e vim-enhanced  #卸载软件，卸载也要解决依赖性，谁依赖你，你把谁给卸载了

```

#### 4、RPM包文件的校验和RPM包本身的校验

```shell
rpm -Va #检测所有已安装的软件设计的所有文件是否被修改过
rpm -V vim-enhanced    #检测该软件的所有文件是否被修改过

rpm -Vf /etc/vimrc    #检测某个文件是否被修改过,然后查这个文件属于哪个包，然后再去还原

[root@bogon ~]# rpm -Vf /etc/vimrc
S.5....T.   c   /etc/vimrc
```

1. 最前面的 8 个字符（S.5....T）都属于验证信息，各字符的具体含义如下：
   - S：文件大小是否改变。
   - M：文件的类型或文件的权限（rwx）是否改变。
   - 5：文件MD5校验和是否改变（可以看成文件内容是否改变）。
   - D：设备的主从代码是否改变。
   - L：文件路径是否改变。
   - U：文件的属主（所有者）是否改变。
   - G：文件的属组是否改变。
   - T：文件的修改时间是否改变。
   - .：若相关项没发生改变，用 . 表示。
2. 被修改文件类型，大致可分为以下几类：
   - c：配置文件（configuration file）。
   - d：普通文档（documentation）。
   - g："鬼"文件（ghost file），很少见，就是该文件不应该被这个 RPM 包包含。
   - l：授权文件（license file）。
   - r：描述文件（read me）。
3. 被修改文件所在绝对路径（包含文件名）。

```shell
rpm --import /efc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7   #安装数字证书，这样再安装软件包的时候，回去校验软件包是否被修改过
rpm -qa | grep gpg-pubkey #查询是否安装数字证书
```

#### 5、提取RPM包中的文件，用于还原发生错误的文件

首先先了解一下cpio这个命令

- cpio如果使用的是绝对路径备份，还原时也要绝对路径，如果使用的是相对路径，还原时也要相对路径。
- cpio 命令无法自行指定备份的文件，需要目标文件（或目录）的完整路径才能成功读取，因此命令常与 find 命令配合使用。
- cpio 命令恢复数据时不会自动覆盖同名文件，也不会创建目录。

```shell
#备份
find /root/test -print | cpio -ocvB > /root/etc.cpio

#还原
cpio -idvcu < /root/etc.cpio   #所有备份文件都还原
cpio -idvcu < /root/etc.cpio /root/test/1 #还原其中1个文件

#复制
find /root/test -print | cpio -p /tmp/test


# 进入到根目录下，执行此命令。
rpm2cpio /media/cdrom/Packages/coreutils-8.22-24.el7.x86_64.rpm | cpio -idv ./usr/bin/ls

# 如果进入到其他目录，后边不能写，这样就把这个包中的所有文件都抽取出来
rpm2cpio /media/cdrom/Packages/coreutils-8.22-24.el7.x86_64.rpm | cpio -idv
```

#### 6、修复RPM包数据库及校验文件的另一种方式

```shell
#RPM包数据库损坏怎么办，删除所有的数据库，重建一个。如果重建数据库报错，去看/var/cache下边有没有缓存
rm -rf /var/lib/rpm/__db.*
rpmdb --rebuilddb

#校验文件的另一种方式
rpm -ql --dump vim-common | grep /etc/vimrc  #可以查看文件的sha256sum码
sha256sum /etc/vimrc						#两者对比，看有没有修改

```

#### 7、SRPM源码二进制包的安装（不常用）

#### 8、YUM简介（RPM管理的升级版）

使用RPM安装，非常不方便，比如安装一个vim，需要是十几个perl包，还要解决各种依赖，非常之麻烦，如果使用YUM的话，只需要一句话即可安装。

在这个目录下边`/etc/yum.repos.d/`，所有以repo结尾的文件都是yum的下载源。

yum的网络源配置

```shell
[base]						#包的容器名称
name=CentOS-$releasever - Base #包名
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra #包的镜像地址，该地址下有非常多的baseurl，几乎涵盖了国内的所有源，取其中最快的一个作为下载源。
#baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/  #指定某一个源地址作为下载
gpgcheck=1  #开启数字证书检验
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7  #数字证书的位置
enabled=1  #使用这个源
```

yum的本地源配置

- 配置本地的源，如果是虚拟机需要注册一个光盘，真实机需要插入光盘。
- 挂载即可

```shell
mkdir -p /media/cdrom
mount -o loop /dev/cdrom /media/cdrom

[local]
name=local
baseurl=file:///media/cdrom/
#file:///media/cdrom/
#file:///media/cdrecorder/
gpgcheck=1
enabled=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

yum源的管理

```shell
#查看所有的yum源列表
yum repolist all

#需要安装yum-utils工具包
yum install -y yum-utils

yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo  #添加一个镜像源
yum-config-manager --enable local		#开启镜像源
yum-config-manager --disable local      #关闭镜像源
				--enable \*			#开启所有
yum clean all                        #清空yum的缓存文件
```

#### 9、YUM命令（重点）

```shell
#查询
yum search all mysql   #查询包是否存在，存在的话就可以安装

yum list vim-enhanced	#查询指定包的安装情况
yum list 				#查询所有已经安装和可以安装的包
installed Packages    	#已经安装的包
net-tools.x86_64     2.0-0.25.20131004git.el7   @base
Available Packages   	#可以安装的包
httpd.x86_64     2.4.6-97.el7.centos        updates

yum info  vim-enhanced		#查询包的具体情况，安装与否，大小，从哪安装的等等

#安装
yum install -y vim			#安装vim软件包

#升级
yum update -y vim			#升级vim包

#卸载
yum remove vim				#卸载vim，貌似只会卸载这个程序，而不会卸载与他有依赖的包
```

### 第三节、源码包的安装

#### 1、简介

- 源码包指的是后缀为 .tar.gz 或 .tar.bz2 的源码包。
- 源码包安装需要编译器gcc，涉及到C++程序的话就需要gcc-c++，自动编译make，常用的还有perl、pcre-devel、expat-devel。
- 使用yum安装这些常用软件或使用rpm去安装也可以的。

#### 2、安装

以安装apache服务器为例来安装

```shell
#安装必备的软件
yum -y install gcc gcc-c++ make pcre-devel expat-devel perl

#去官网下载.tar.gz包，下载的包一般都放在/usr/local/src目录下
http://httpd.apache.org/download.cgi#apache24

#解压
tar zxvf httpd-2.4.46.tar.gz

#进行安装之前，需要先下载apr和apr-util。安装任何软件之前，最好阅读一下它的readme或install文件

#下载apr和apr-util，并解压到 /usr/local/src/httpd-2.4.46/srclib 即可

#编译前的准备工作
./configure --help #查看一下需要用到哪些模块和指定一下目录

#做准备
./configure \
--prefix=/usr/local/apache2/ \       #指定安装路径
--enable-so \                     #启用动态加载模块
--enable-rewrite \                 #支持网站地址重写
--enable-charset-lite \              #启用字符集支持
--enable-cgi                      #启用CGI脚本程序支持

#安装
make && make install

#由于源码安装，是指定的路径，所以启动的话，也要自己搞
cp /usr/local/apache2/bin/apachectl /etc/init.d/httpd  #复制一份出来，放入到service里头进行管理

```

#### 3、卸载

源码包安装所有的文件都放在同一个目录下，卸载也非常好卸载。

卸载之前，首先要把服务关掉。

```shell
rm -rf /usr/local/apache2/
```

### 第四节、两者的比较

1. 能用yum安装就用yum安装，比较方便，也能查询系统安装了哪些软件，也有数字证书验证，出了故障可以解决的。
2. 如果有的软件不支持rpm的话，那就只能自己源码安装了。







