### 一、初识linux

```markdown
terminals 和 console 是连接linux服务器的软件。有很多个软件。shell 是与linux内核交互的中间层，有很多种shell，Bourne shell检出sh，有bourne again shell简称bash（最流行的），有Z shell简称zsh（下一代最流行）等。

linux command的结构
ls -a /root
ls 命令
-a  短参数，--all是长参数，两者功能都是一样的，
	只是短参数比较短并且多个短参数可以写在一个里头，ls -alh
	短参数需要赋值时  date -s '2022-08-29'
    长参数需要赋值时  date --string='2022-08-29'
/root 参数，命令需要操作的对象    
```

```shell
# 查看当前的用户及用户组
id

# 查看内核版本
uname -a
# 查看系统版本
cat /etc/redhat-release  # 适用于Redhat系列
cat /etc/issue           # 适用于所有linux发行版

# 查看当前日期
date
date '+%Y-%m-%d %H:%M:%S'
date -s '2022-08-29 13:38:00' # 或date date 082913372021.15 设置系统时间
# 查看当前日历
cal
cal -y 2022
```

### 二、帮助命令的使用

```shell
# 查看命令的类型，有内置的和外部安装的
# 如果是内置的使用 help cd | cd --help
# 如果是外置的使用 man date | date --help
type ls
```

```shell
# man 命令查看帮助命令时，使用less命令进行查看
man ls
man -k contents  # 搜索命令|命令简介在哪命令里头
apropos contents # 搜索命令|命令简介在哪命令里头
# less查看文件时，常用的操作
# SPACE | b   向下翻一页|往上翻一页
# d | u       向下翻半页|往上翻半页
# g | shift + g 跳转开头|跳转结尾
# /search     搜索 n | shift + n 向下搜索|向上搜索 
```

### 三、常用快捷键

```shell
# tab 命令|目录|文件自动补全功能
# ctrl + l 清屏
# ctrl + d 退出shell
# ctrl + a 回到命令行开头
# ctrl + e 回到命令行结尾
# ctrl + r 搜索之前用到的命令，按左右键可以搞到屏幕上去或使用 ctrl + k 剪切走
# ctrl + g 从搜索中跳出来
# ctrl + k 剪切，光标到结尾的内容
# ctrl + u 剪切，光标到开头的内容
# ctrl + y 粘贴
# ctrl + c 退出当前进程|中断执行命令
# ctrl + z 把当前进程暂停执行并放到后台，使用fg命令恢复
```

### 四、历史命令

```shell
# 历史命令文件都保存在 ~/.bash_history中，最多可存 echo $HISTFILESIZE 条命令，只有退出之后操作的命令才会保存到文件中
cat ~/.bash_history
# 历史命令内存，内存当中总共可以存echo $HISTSIZE条命令
history         # 查看内存历史命令
history -d 10   # 删除第10条内存历史命令
history -c      # 清空内存历史命令，清空之后，再次退出，不会保存到文件当中
# 执行历史命令
!!    # 执行上一条命令
!-2   # 执行倒数第2条命令
!998  # 执行第998条命令
!ping # 执行最后一次ping的命令，这些操作都很危险，最好不要这么做，除非你知道你执行哪一条命令
!ping:p # 将这条命令打印在屏幕上，不执行。
```

```sh
# 历史命令忽略，不想让别人知道你操作了什么
# 可以使用history -c进行清除，这样有点奇怪，你做了什么操作不像让人知道啊
# 也可以vim .bash_history去清除，但这样也很奇怪，别人会看到你编辑了这个文件
HISTCONTROL=ignoredups      # 忽略多次重复执行的命令，只记录1条
HISTCONTROL=ignorespace     # 忽略以空格开头的命令，不记录
HISTCONTROL=ignoreboth      # 两者都忽略 
```

```shell
# 历史命令时间记录
HISTTIMEFORMAT="%Y-%m-%d %T"

echo "HISTTIMEFORMAT="%Y-%m-%d %T"" >> .bashrc
echo "HISTCONTROL=ignoreboth" >> .bashrc
```

#### 五、root与其他用户

```shell
# sudo命令是ubuntu系统特有的，在centos中，只有su命令。
# 从root切换到非root用户，不需要密码，直接切换即可
# - 表示重新登录shell把环境变量都切换一下，不带-也可以，可能有点问题。
su - liuyajie
# 普通用户切换到root用户，需要填写密码
su -

# 只有root用户可以修改其他人的密码，其他人只能自己修改自己的密码
passwd liuyajie
```

























