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



#### 7、SRPM源码二进制包的安装（不常用）

#### 8、YUM简介（RPM管理的升级版）

#### 9、YUM源配置

#### 10、YUM命令（重点）

### 第三节、源码包的安装



