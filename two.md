



#### 命令格式

```
命令格式：

主命令 选项 操作对象
```

<img src="C:\Users\xgz24\AppData\Roaming\Typora\typora-user-images\image-20261007133059007.png" alt="image-20261007133059007" style="zoom:67%;" />

*   命令分为两类：

​	内置命令：由shell自带的命令。速度快

​	内置命令：是shell程序自身组成部分，存储于shell内部代码。用户输入内置命令，shell直接识别并执行对应代码逻辑，无需在PATH 环境变量路径搜索，也不创建新进程，在当前shell环境作用域内执行，执行效率高、占用资源少。

​	外置命令：有独立的可执行程序文件，执行时需要从磁盘加载对应的程序文件，文件名通常就是命令名

​	外置命令：以独立可执行二进制文件存于系统文件系统。用户输入外置命令，系统依据PATH 环境变量记录路径搜索同名二进制文件。找到后创建新进程，将可执行文件加载到新进程内存空间执行，执行完进程关闭，能跨环境使用，但执行开销相对大。



*   选项：指定命令的运行特性，指明要运行命令中的哪一个功能代码

​	短选项：如 -l -d（如果同一命令同时使用多个短选项，多数可合并，比如-df）            [注意：有些选项没有 -]

​	长选项：如 --help



执行内置命令的流程

*   shell认识，直接执行就行了

执行外置命令的流程

*   shell解释器会找文件，找到了与命令同名的文件，就执行
*   没找到文件的话，shell告诉你没找到该文件。

```
[root@rhcsa ~]# echo $PATH
/root/.local/bin:/root/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin
会在这五个路径里面找文件（冒号分隔）
```











#### 查看命令类型

——————type

查看命令类型是内置命令、外置命令、alias 别名

​	内置命令：shell 为了完成自我管理和基本的管理，不同的 shell 内置不同的命令，但是大部分都差不多，没有独立的可执行文件

​	外置命令：存放在 /bin 等目录、有独立可执行文件的外部程序

​	alias 别名：给现有命令起的快捷昵称

​	



格式

```
type -参数 命令名
```



参数：

| 选项 | 功能                                                         |
| ---- | ------------------------------------------------------------ |
| -a   | 显示命令所有信息（别名、类型）                               |
| -t   | 只显示命令类型（alias（别名）、builtin（内置命令）、file（外部可执行文件）） |

<img src="C:\Users\xgz24\AppData\Roaming\Typora\typora-user-images\image-20261007144817034.png" alt="image-20261007144817034" style="zoom:67%;" />

<img src="C:\Users\xgz24\AppData\Roaming\Typora\typora-user-images\image-20261007145625614.png" alt="image-20261007145625614" style="zoom:67%;" />

<img src="C:\Users\xgz24\AppData\Roaming\Typora\typora-user-images\image-20261007145718632.png" alt="image-20261007145718632" style="zoom:67%;" />







#### 特殊符号



|    特殊符号     | 功能                                        |
| :-------------: | :------------------------------------------ |
|        ;        | 分隔多条命令                                |
|        *        | 匹配任意零个或多个字符                      |
|        ?        | 匹配任意单个字符                            |
|       []        | 匹配方括号里任意一个字符（表示范围用 -）    |
|  [^] 或者 [!]   | 匹配方括号里任意一个字符取反                |
| {string,string} | 匹配花括号里任意一个字符串（表示范围用 ..） |







#### 设置别名





可以看下系统里面所有的别名

![image-20261007151252149](C:\Users\xgz24\AppData\Roaming\Typora\typora-user-images\image-20261007151252149.png)



```
[root@rhcsa ~]# alias ls='ls --color=auto'
设置别名（就像这样设置就好）

alias 别名='原命令-参数'
```









```
[root@rhcsa ~]# alias pingg='ping -c 3'
设置ping -c 3的别名为pingg
```

![image-20261007151753861](C:\Users\xgz24\AppData\Roaming\Typora\typora-user-images\image-20261007151753861.png)





```
[root@rhcsa ~]# unalias pingg
删除别名
```



以上敲的命令都是临时的

以上设置别名的方法是只有当前 shell 有效，重新开启一个 shell 或者重新登陆系统，以上别名则会失效







alias 永久化的方法：

把别名的设置命令写入配置文件中

​	~/.bashrc——————————————————————仅对当前用户生效，永久生效（每打开shell都会加载）

​	/etc/bashrc—————————————————————对所有用户生效，永久生效

​	~/.bash_profile———————————————————仅对当前用户生效，仅在登录的时候加载一次

​	/etc/profile——————————————————————对所有用户生效，仅在登录的时候加载一次



直接去编辑路径里面的这个文件就行了



>   ~仅对当前用户生效
>
>   /etc对所有用户生效
>
>   bashrc永久生效
>
>   profile仅在登录的时候加载一次，source ~/.bash_profile和source /etc/profile可以手动加载



==**Linux一切都是文件**==









#### 历史命令



```
[root@rhcsa ~]# history
展示所有历史记录

[root@rhcsa ~]# history 10
展示最近十条命令

[root@rhcsa ~]# history -c
清空历史记录

[root@rhcsa ~]# !46
执行46号历史命令
```

总共会保存最近的1000条命令，可以在/etc/profile文件里进行修改





#### 命令帮助



```
[root@rhcsa ~]# help echo
查看内置命令echo

[root@rhcsa ~]# ls --help
查看外置命令ls（--help成长选项了）绝大多数命令都能看，包括内置
```



```
[root@rhcsa ~]# man cd
查看帮助手册，按q键退出
```

最常用、最核心的 Linux 帮助文档，全称 manual（手册），几乎覆盖所有命令 / 程序 / 系统调用。

内容结构化、全面，包含命令的所有选项、参数、示例、注意事项，甚至相关命令参考。

手册页分章节（比如 1 = 用户命令，5 = 配置文件，8 = 系统管理命令），可以指定章节查看：man 5 passwd（查看 passwd 配置文件的手册）。

进入专门的阅读界面，支持翻页、搜索（按 / 输入关键词），按 q 退出。











```
[root@server ~]# man -f passwd——————————————————————查看passwd的所有章节
passwd (5) - password file
passwd (1ssl) - OpenSSL application commands
passwd (1) - update user's authentication tokens

[root@rhcsa ~]# man 1 passwd
[root@rhcsa ~]# man 5 passwd——————————————————打开章节5
```

man 手册的章节划分
1：用户命令
2：系统调用，查看可被内核调用的函数的帮助
3：程序库调用，查看函数和函数库的帮助
4：设备文件（主要是 /dev 目录下的文件）
5：配置文件格式，查看配置文件的帮助
6：游戏，查看游戏的帮助
7：杂项，惯例与协议等，例如 Linux 文件系统、网络协议、ASCII code 等等的说明
8：系统指令，查看系统管理员可用的命令帮助
9：内核内部指令，查看内核相关文件帮助

>   如果 man -f passwd 命令查看不了，则使用下述命令重新生成手册页索引数据库即可
>
>   [root@rhcsa ~]# mandb





man手册常用按键

|       按键        | 功能                                              |
| :---------------: | :------------------------------------------------ |
|         ↑         | 向上翻一行                                        |
|         ↓         | 向下翻一行                                        |
| 空格键、PaGe down | 向下翻一页                                        |
|    b、PaGe up     | 向上翻一页                                        |
|       home        | 前往首页                                          |
|        end        | 前往尾页                                          |
|         /         | 基于当前位置，从上至下搜索某个关键词，如“/linux”  |
|         ?         | 基于当前位置，从下至上搜索某个关键词，如“?/linux” |
|         n         | 定位到下一个搜索到的关键词                        |
|         N         | 定位到上一个搜索到的关键词                        |
|         q         | 退出                                              |



我想你估计压根用不到这个表格

|  结构名称   | 代表含义                                     |
| :---------: | :------------------------------------------- |
|    NAME     | 命令的名称                                   |
|  SYNOPSIS   | 命令的语法格式                               |
| DESCRIPTION | 命令的核心功能                               |
|  EXAMPLES   | 演示（附带简单说明）                         |
|  OVERVIEW   | 概述                                         |
|  DEFAULTS   | 默认的功能                                   |
|   OPTIONS   | 具体的可用选项（带介绍）                     |
| ENVIRONMENT | 环境变量                                     |
|    FILES    | 命令运行时依赖或操作的配置文件、数据文件路径 |
|  SEE ALSO   | 相关的资料                                   |
|   HISTORY   | 维护历史与联系方式                           |





#### 时间设置





```
[root@rhcsa ~]# cal
查看当前时间

[root@rhcsa ~]# cal 2 2026
查看2026年二月

[root@rhcsa ~]# cal 18 2 2026
查看2026年2月18日
```









==**timedatectl类**==

| 参数           | 作用         |
| :------------- | :----------- |
| status         | 显示状态     |
| list-timezones | 显示所有时区 |
| set-timezone   | 设置时区     |
| set-time       | 设置日期时间 |
| set-ntp        | 设置时间同步 |

```
[root@rhcsa ~]# timedatectl status————————————————————————————————————————————查看当前系统时间与时区

Local time: Fri 2025-12-26 15:04:37 CST # 本地时间
Universal time: Fri 2025-12-26 07:04:37 UTC # 世界时间
RTC time: Fri 2025-12-26 07:04:36 # 硬件时间
Time zone: Asia/Shanghai (CST, +0800) # 时区
System clock synchronized: yes # 是否时间同步
NTP service: active # 时间服务器状态
RTC in local TZ: no # 硬件时间是否使用本地时间


[root@rhcsa ~]# timedatectl set-timezone Asia/chongqing————————————————————————设置时区为亚洲重庆

[root@rhcsa ~]# timedatectl set-ntp no—————————————————————————————————————————关闭时间同步

[root@rhcsa ~]# timedatectl set-time 2018-7-1——————————————————————————————————设置日期

[root@rhcsa ~]# timedatectl set-time 12:00:01——————————————————————————————————设置时间

[root@rhcsa ~]# timedatectl set-time '2020-01-01 8:30'

[root@rhcsa ~]# timedatectl set-ntp yes—————————————————————————————————————————开启时间同步
```









```
[root@rhcsa ~]# date——————————————————————————————————查看当前时间

显示和设置时间的格式

[root@rhcsa ~]# date +"%Y %m"—————————————————————————设置日期格式

[root@rhcsa ~]# date +"%Y/%m/%d %H:%M"————————————————设置日期格式



[root@rhcsa ~]# date 011010102026.01——————————————————修改日期，月日十分年.秒（不加选项就按照这个来）

[root@rhcsa ~]# date -s "20261007 12:00"——————————————我更推荐这样修改

[root@rhcsa ~]# date -d "+10 min"—————————————————————日期推算，看看10min以后是什么时间
[root@rhcsa ~]# date -d "-10 day"—————————————————————一样的道理
```

| 参数 | 作用                                             |
| :--: | :----------------------------------------------- |
|  %Y  | 完整年份                                         |
|  %m  | 月份                                             |
|  %d  | 日期                                             |
|  %H  | 小时                                             |
|  %M  | 分钟                                             |
|  %S  | 秒钟                                             |
|  %X  | %H:%M:%S AM/PM（12 小时制，以 AM/PM 区分上下午） |
|  %Z  | 时区                                             |
|  %a  | 星期（缩写）                                     |
|  %A  | 星期（全称）                                     |
|  %p  | AM/PM                                            |
|  %j  | 当年中的第几天                                   |







#### 查看当前目录所有文件



```
[root@rhcsa ~]# ls————————————————————————————查看当前目录

[root@rhcsa ~]# ls / —————————————————————————查看当前目录的根目录

[root@rhcsa ~]# ls /etc/——————————————————————查看当前目录的根目录里的etc目录

[root@rhcsa ~]# ls -lh————————————————————————平铺并且看到文件大小单位
```

| 参数 | 功能（这些功能是可以叠加使用的）                             |
| :--- | :----------------------------------------------------------- |
| -l   | 长格式显示文件/目录的详细信息                                |
| -d   | 显示目录本身的信息                                           |
| -h   | 人类易读显示（文件大小加上 K、M 等单位，需要与 -l 结合使用） |
| -S   | 按照文件大小排序显示                                         |
| -a   | 显示所有文件（包括隐藏文件）（包括 . 和 ..）                 |
| -A   | 显示所有文件（包括隐藏文件）（不包括 . 和 ..）               |
| -i   | 显示文件索引号（inode）                                      |
| -r   | 逆序                                                         |
| -R   | 递归显示目录及子目录的所有内容                               |



ls 浏览后颜色表示：

​	白色：普通文件

​	蓝色：目录

​	绿色：可执行文件

​	红色：压缩包文件

​	黄色：块文件（就是设备文件）

​	青色：软连接文件（就是一个链接，相当于Windows的快捷方式）





#### 文件类型（ls）



| 字符 |      文件类型      | 说明                                                         |
| :--: | :----------------: | :----------------------------------------------------------- |
|  -   |      普通文件      | 类似于 Windows 的记事本                                      |
|  d   |      目录文件      | 类似于 Windows 的文件夹                                      |
|  c   |    字符设备文件    | 串行端口设备，顺序读写，键盘                                 |
|  b   |     块设备文件     | 可供存储的接口设备，随机读写，硬盘                           |
|  p   |      管道文件      | 本机进程间通信的接口                                         |
|  s   |     套接字文件     | 通常用于网络上的通信。可以启动一个程序来监听客户端的要求，客户端可以通过套接字来进行数据通信 |
|  l   | 软链接（符号链接） | 类似于 Windows 的快捷方式                                    |
|      |       硬链接       | 文件别名                                                     |

<img src="C:/Users/xgz24/AppData/Roaming/Typora/typora-user-images/image-20261007205427060.png" alt="image-20261007205427060" style="zoom:67%;" />

就是这个东西



```
[root@rhcsa ~]# ll anaconda-ks.cfg
-rw-------. 1 root root 1080 Dec 24  2025 anaconda-ks.cfg
[root@rhcsa ~]# ll -d /home/
drwxr-xr-x. 3 root root 17 Dec 24  2025 /home/
[root@rhcsa ~]# ll /dev/tty
crw-rw-rw- 1 root tty 5, 0 Dec 26  2025 /dev/tty
[root@rhcsa ~]# ll /dev/nvme0n1
brw-rw---- 1 root disk 259, 0 Dec 26  2025 /dev/nvme0n1
[root@rhcsa ~]# ll /var/run/chrony/chronyd.sock
srwxr-xr-x 1 chrony chrony 0 Jan  1 08:30 /var/run/chrony/chronyd.sock
[root@rhcsa ~]# ll /run/initctl
prw------- 1 root root 0 Feb 27  2026 /run/initctl
[root@rhcsa ~]# ll /usr/bin/yum
lrwxrwxrwx. 1 root root 5 Jun 29  2023 /usr/bin/yum -> dnf-3

有兴趣自己找找吧
```







####  查看文件类型（file）





```
[root@rhcsa ~]# file /etc/ ——————————————————查看根目录下的etc的文件属性
/etc/: directory

[root@rhcsa ~]# file anaconda-ks.cfg—————————查看当前目录下的anaconda-ks.cfg文件
anaconda-ks.cfg: ASCII text
```



ASCII file：ASCII 文本字符文件

block special：块设备文件

character special：字符设备文件

directory：目录文件

fifo：管道文件

socket：套接字文件

symbolic link：软连接文件

empty：空文件





#### 根目录结构和作用



**FHS**



概念：

​	filesystem hierarchy standard 文件系统层级标准，定义了在类 Unix 系统中的目录结构和目录内容，即让用户了解到已安装软件通常放置于哪个目录下。



Linux 目录结构的特点：

​	使用树形目录结构来组织和管理文件。

​	整个系统只有一个根目录（树根），Linux 的根目录用“/”表示

​	其他所有分区以及外部设备（如硬盘、光驱等）都是以根目录为起点，挂接在目录树的某个目录中的，通过访问挂载点目录，即可实现对这些分区的访问。

<img src="C:/Users/xgz24/AppData/Roaming/Typora/typora-user-images/image-20261007220713472.png" alt="image-20261007220713472" style="zoom:67%;" />











<img src="C:/Users/xgz24/AppData/Roaming/Typora/typora-user-images/image-20261007215355620.png" alt="image-20261007215355620" style="zoom:80%;" />

|     目录名     | 描述                                                         |
| :------------: | :----------------------------------------------------------- |
|     **/**      | Linux 文件系统的最上层根目录，其他所有目录均是该目录的子目录 |
|   **/boot**    | 存放系统启动时所需的文件，这些文件若损坏常会导致系统无法启动，一般不要改动（有些像C盘） |
|   **/root**    | 超级用户的个人目录，普通用户没有权限访问                     |
|   **/home**    | 存放一般用户的个人目录                                       |
|    **/bin**    | Binary 的缩写，存放普通用户可执行的程序或命令                |
|     /sbin      | 和 /bin 类似，这些文件往往用来进行系统管理，只有 root 可使用 |
|      /lib      | 是库（library）英文缩写，存放系统的各种库文件                |
|     /lib64     | 存放系统本身需要用到 64 位程序的共享函数库（library）        |
|    **/usr**    | 一般用户应用程序安装目录，用于安装各种应用程序               |
|      /opt      | 该目录通常提供给较大型的第三方应用程序使用，例如 Sun Staroffice、Corel WordPerfect 这可避免将文件分散至整个文件系统 |
|    **/etc**    | 存放了系统管理时要用到的各种配置文件和子目录                 |
|    **/var**    | 通常各种系统日志文件放在这里                                 |
|      /run      | 保存自系统启动以来描述系统信息的文件                         |
|    **/dev**    | dev 是设备（device）的英文缩写。包含所有的设备文件           |
| /mnt 和 /media | 可以临时将别的文件系统挂在这个目录下，即为其他的文件系统提供安装点 |
|      /tmp      | 用来存放不同程序执行时产生的临时文件                         |
|      /srv      | srv 是服务（server）的简写，服务启动之后需要访问的数据目录   |
|      /sys      | 本目录是将内核的一些信息映射文件，硬件相关的信息             |
|     /proc      | 可以在这个目录下获取系统状态信息，详解网址 https://mp.weixin.qq.com/s/4WUJGySmSYPapJKgTvRD1w |



注意：

​	系统自带的目录不要随意刪除

​	目录的名称是区分大小写的

​	FHS标准并不是一种强制约束标准，是一种经验的总结，应灵活使用







#### 路径和工作目录



用“路径”来表示某个文件（或目录）在目录结构中所处的位置。顾名思义，路径是指从树型目录中的某个目录层次到达某一文件或子目录的一条线路，路径由以“”为分隔符的多个目录名构成。（八股）



绝对路径：/etc/ssh/sshd_config.d

相对路径：我已经在/etc/里边了，ssh/sshd_config.d


```
[root@rhcsa ~]# pwd——————————————————————————————————————查看当前路径
/root

[root@rhcsa ~]# cd /etc/—————————————————————————————————到达/etc/，用的绝对路径
[root@rhcsa etc]#

[root@rhcsa ssh]# cd sshd_config.d/————————————————————————到达/etc/ssh/sshd_config.d/，用的相对路径，简直拉完了

[root@rhcsa ssh]# cd——————————————————————————————————————代表回到当前用户的家目录
[root@rhcsa ssh]# cd ~ ———————————————————————————————————代表回到当前用户的家目录
[root@rhcsa ssh]# cd /root ———————————————————————————————代表回到当前用户的家目录

[root@rhcsa etc]# cd .. ——————————————————————————————————返回当前目录的上一级目录
[root@rhcsa /]# cd

[root@rhcsa etc]# cd . ———————————————————————————————————返回当前目录

[root@rhcsa etc]# cd - ———————————————————————————————————回到之前的目录（有点ctrl+z的感觉）
```

















































