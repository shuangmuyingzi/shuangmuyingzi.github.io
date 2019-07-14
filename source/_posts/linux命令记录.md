title: Linux
date: 2019-05-28
tag:
 - js

photos:
 - /img/2017/6.jpg

---

因为要查看一些用接口补获的错误日志，因此就要进对应的项目服务器里面去查看，自然Linux并不像window系统那样便利的图形化操作，因此我们要就学会几个linux命令啦。

<!--more-->

### 为什么我们要学习Linux
相信大部分人的PC端都是用Windows系统的，那我们为什么要学习Linux这个操作系统呢？？？Windows图形化界面做得这么好，日常基本使用的话，学习成本几乎为零。

而Linux不一样，可能刚接触Linux的人会认为：Linux好麻烦哦，不好玩，都是字符界面。不直观、这个破系统是用来干嘛的~~

日常用的话Windows是比较顺手的，但是我们要知道的是：我们开发出来的程序一般都是放在Linux下运行的。

那可能就会有人提出疑问了：Windows同样是操作系统，为啥要放在Linux下，而不放在Windows下呢？？相信Windows也是可以运行我们写出来的程序的。

我总结了Linux的几个优点：

1、免费
2、很多软件原生是在Linux下运行的，庞大的社区支持，生态环境好。
3、开源，可被定制，开放，多用户的网络操作系统。
4、相对安全稳定

所以开发者选择了Linux来跑我们自己写出来的程序。
### Linux的基础知识
Linux系统的组成：

1、linux内核（linus 团队管理）
2、shell：用户与内核交互的接口
3、文件系统：ext3、ext4等。windows 有 fat32 、ntfs
4、第三方应用软件

### Linux基本目录结构
Linux 文件系统是一个目录树的结构，文件系统结构从一个根目录开始，根目录下可以有任意多个文件和子目录，子目录中又可以有任意多个文件和子目录

bin 存放二进制可执行文件(ls,cat,mkdir等)
boot 存放用于系统引导时使用的各种文件
dev 用于存放设备文件
etc 存放系统配置文件
home 存放所有用户文件的根目录
lib 存放跟文件系统中的程序运行所需要的共享库及内核模块
mnt 系统管理员安装临时文件系统的安装点
opt 额外安装的可选应用程序包所放置的位置
proc 虚拟文件系统，存放当前内存的映射
root 超级用户目录
sbin 存放二进制可执行文件，只有root才能访问
tmp 用于存放各种临时文件
usr 用于存放系统应用程序，比较重要的目录/usr/local 本地管理员软件安装目录
var 用于存放运行时需要改变数据的文件
### 接触的命令
tips:输入命令的时候要常用tab键来补全

ls：显示文件或目录信息
mkdir：当前目录下创建一个空目录
rmdir：要求目录为空
touch：生成一个空文件或更改文件的时间
cp：复制文件或目录
mv：移动文件或目录、文件或目录改名
rm：删除文件或目录
ln：建立链接文件
find：查找文件
file/stat：查看文件类型或文件属性信息
cat：查看文本文件内容
more：可以分页看
less：不仅可以分页，还可以方便地搜索，回翻等操作
tail -10： 查看文件的尾部的10行
head -20：查看文件的头部20行
echo：把内容重定向到指定的文件中 ，有则打开，无则创建
管道命令 | ：将前面的结果给后面的命令，例如：ls -la | wc，将ls的结果加油wc命令来统计字数
重定向 > 是覆盖模式，>> 是追加模式，例如：echo "Java3y,zhen de hen xihuan ni" > qingshu.txt把左边的输出放到右边的文件里去
cat 文件名
预览文件

tail -f 文件名
实时监听文件的变化

grep命令
grep(global search regular expression)是一个强大的文本搜索工具。grep 使用正则表达式搜索文本，并把匹配的行打印出来。
格式：grep [options] PATTERN [FILE...]

PATTERN 是查找条件：可以是普通字符串、可以是正则表达式，通常用单引号将RE括起来。
FILE 是要查找的文件，可以是用空格间隔的多个文件，也可是使用Shell的通配符在多个文件中查找PATTERN，省略时表示在标准输入中查找。
grep命令不会对输入文件进行任何修改或影响，可以使用输出重定向将结果存为文件
例子：

在文件 myfile 中查找包含字符串 mystr的行
grep -n mystr myfile
显示 myfile 中第一个字符为字母的所有行
grep '^[a-zA-Z]' myfile
在文件 myfile 中查找首字符不是 # 的行（即过滤掉注释行）
grep -v '^#' myfile
列出/etc目录（包括子目录）下所有文件内容中包含字符串“root”的文件名
grep -lr root /etc/*


用grep查找/etc/passwd文件中以a开头的行，要求显示行号；查找/etc/passwd文件中以login结束的行；

grep -i "15:00" 文件名
 
grep -i "15:00" /media/raid10/htdocs/项目名/runtime/logs/logs-report-2019-07-01







