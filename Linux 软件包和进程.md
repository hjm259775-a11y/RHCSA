# 管理 Linux 软件包



## 软件包管理



### RPM



​	RPM（Redhat Package Manager）由 Red Hat 公司提出的一种软件包管理标准

​	是 Linux 各发行版中应用最广泛的软件包格式之一（还有 debian 的发行版 deb 安装包）

​	产生原因：软件制作者在其工作环境下将软件编译完毕后再进行发布，以此减少安装编译时间，要求：软件安装环境与编译时的环境要相同



RPM 安装软件要求：

​	软件安装环境必须与打包软件环境一致

​	必须满足软件依赖性

​	软件卸载时必须小心，最底层软件不能先卸载



rpm 软件包命名格式：

​	含义：软件名-版本号-发行号.操作系统版本.硬件平台的类型.rpm

​	如：zsh-5.0.2-14.el7.x86_64.rpm

| zsh    | -5       | 0        | 2      | 14           | el7          | x86_64                    |
| ------ | -------- | -------- | ------ | ------------ | ------------ | ------------------------- |
| 软件名 | 主版本号 | 次版本号 | 修订号 | 软件发布次数 | 发行商 RHEL7 | CPU 架构平台 支持系统位数 |

rpm 包相关网站：

​	http://rpmfind.net/

​	http://rpm.pbone.net/





#### rpm软件安装



```
rpm -选项 软件包名 （一般都是-ivh，-evh，-Uvh）
```

|     参数      | 功能             |
| :-----------: | ---------------- |
| -i, --install | 安装软件包       |
|      -v       | 显示详细信息输出 |
|      -U       | 更新软件包       |
|      -e       | 卸载软件包       |
|      -h       | 显示安装进度     |



```
[root@rhcsa ~]# wget https://qqdl.gtimq.cn/qqfile/QQNT/9.9.33/release/3f89efc5/QQ_3.2.32_260812_x86_64_01.rpm

# 缺少依赖软件
[root@localhost ~]# rpm -ivh QQ_3.2.29_260528_x86_64_01.rpm
错误：依赖检测失败：
        libXScrnSaver 被 linuxqq-3.2.29_49738-1.x86_64 需要
        xdg-utils 被 linuxqq-3.2.29_49738-1.x86_64 需要

# 下载依赖软件
# 在 http://rpmfind.net/ 网站下载所需软件
[root@rhcsa ~]# wget http://rpmfind.net/linux/epel/10.2/Everything/x86_64/Packages/l/libXScrnSaver-1.2.4-1.el10_1.x86_64.rpm

[root@rhcsa ~]# wget http://rpmfind.net/linux/almalinux/10.2/AppStream/x86_64/os/Packages/xdg-utils-1.2.0-4.el10.noarch.rpm

[root@rhcsa ~]# rpm -ivh libXScrnSaver-1.2.3-10.el9.x86_64.rpm

[root@rhcsa ~]# rpm -ivh xdg-utils-1.2.0-4.el10.noarch.rpm ——————————————————————————————把这两个依赖先装了

[root@rhcsa ~]# rpm -ivh QQ_3.2.22_251203_x86_64_01.rpm —————————————————————————————————安装QQ

```













#### rpm查询功能



```
rpm -q 软件包名
```

-   -q 查询系统中已安装的软件包
-   -q 与下述选项连用

| 选项 | 功能                               |
| ---- | ---------------------------------- |
| -a   | 查询所有已安装软件包               |
| -l   | 显示已安装软件包的相关文件列表信息 |
| -f   | 查询该文件属于哪个已安装的软件包   |
| -i   | 显示已安装软件包的详细信息         |





```
# 查询已安装 QQ 的软件包名（软件包名并非运行命令）
[root@rhcsa ~]# rpm -qa | grep qq

# 列出 QQ 软件相关文件列表
[root@rhcsa ~]# rpm -ql linuxqq ————————————————————————————————这个linuxqq就是软件包名

# 运行
[root@rhcsa ~]# /opt/QQ/qq
```





### YUM/DNF



YUM (Yellow dog Updater, Modified)

RHEL9 中默认使用的软件批量管理工具由原版本的 yum 换成了速度更快的 dnf (DNF = Dandified YUM)，原有的 yum 命令仅为 dnf 的软链接，当然依旧可以使用。

```
[root@rhcsa ~]# which yum
/usr/bin/yum
[root@rhcsa ~]# ll /usr/bin/yum
lrwxrwxrwx. 1 root root 5 Jun 29  2023 /usr/bin/yum -> dnf-3
```

YUM/DNF 功能：

​	在线下载、安装、卸载、升级 rpm 软件包

​	自动查找并解决 rpm 包之间的依赖关系，一次性安装所有具有依赖关系的 rpm 包，而无需管理员逐个、手工地去安装每一个 rpm 包（！？强强？！，这就是他比RPM强的地方）





**工作原理**：

-   用户发起安装请求
    -   用户在本机输入 yum install 软件名后，YUM 会先查本机 RPM 数据库，确认这个软件是否已经装过。
-   远程仓库比对依赖
    -   如果没装，YUM 会连接YUM 服务器的软件仓库，下载仓库里的"软件依赖关系列表"，然后和本机已装软件（查 RPM 数据库）对比，算出"需要下载的软件包 + 所有缺失的依赖包"。
-   自动下载安装
    -   YUM 从 YUM 服务器下载对应的所有软件包，按依赖顺序自动安装，最后更新本机的 RPM 数据库（记录新安装的软件）。

（其实就是要下载某一个软件时，先对比本地rpm数据库，查看是否安装，之后再去yum软件仓库下载依赖关系列表，将此列表和自己本地的RPM数据库作对比，安装自己没有的依赖包，然后再安装自己要下载的软件）





系统怎么找 YUM 服务器的软件仓库

-   YUM 会依据 /etc/yum.repos.d/ 目录下以 .repo 结尾文件中配置的地址，连接到 YUM 服务器的软件仓库
    -   RHEL 7：仅有一个主要软件仓库。
    -   RHEL 8 及之后：包含两个软件仓库，分别为：
-   BaseOS 存储库：提供一套核心的底层操作系统功能，作为基础软件安装库，例如提供内核、基础系统工具等软件包。
-   AppStream 存储库：包含额外的用户空间应用程序（如办公软件、图形界面工具等）、运行时语言（如 Python、Java 等运行时环境）以及数据库（如 MySQL、PostgreSQL 等数据库管理系统）。



#### 本地临时挂载源

```
# 挂载光盘到 /mnt 目录下
# 注意：此命令是临时挂载，下一次重启系统即失效
[root@rhcsa ~]# mount /dev/sr0 /mnt
mount: /mnt: WARNING: source write-protected, mounted read-only.

# 注意：rhel7 之后就存在了两个 yum 源，分别是 BaseOS 和 AppStream
[root@rhcsa ~]# ls /mnt/
AppStream  EFI               Flatpaks  media.repo
BaseOS     EULA              GPL       RPM-GPG-KEY-redhat-beta
boot       extra_files.json  images    RPM-GPG-KEY-redhat-release

# 建立本地 yum 源配置文件
[root@rhcsa yum.repos.d]# vim /etc/yum.repos.d/rhel10.repo

[BaseOS]
name=BaseOS
baseurl=file:///mnt/BaseOS
gpgcheck=0

[AppStream]
name=AppStream
baseurl=file:///mnt/AppStream
gpgcheck=0

# 清空缓存
[root@rhcsa ~]# dnf clean all

# 生成新的 yum 缓存
[root@rhcsa ~]# dnf makecache

# 测试：下载软件
[root@rhcsa ~]# dnf install gcc -y ——————————————————————————————————————————下载软件
```

-   如果安装软件时会卡住，等待很久才出现安装消息
-   则需在 /etc/hosts 文件中添加本地主机名



```
[root@rhcsa530 ~]# vim /etc/hosts
# Loopback entries; do not change.
# For historical reasons, localhost precedes localhost.localdomain:
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 rhcsa530    # 最后添加自己的主机名
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
# See hosts(5) for proper format and other examples:
# 192.168.1.10 foo.example.org foo
# 192.168.1.13 bar.example.org bar
```





#### 本地永久挂载源

```
# 修改系统开机会自动运行的脚本
# 最后一行添加 mount 挂载命令
[root@rhcsa ~]# vim /etc/rc.d/rc.local
#!/bin/bash
# THIS FILE IS ADDED FOR COMPATIBILITY PURPOSES
#
# It is highly advisable to create own systemd services or udev rules
# to run scripts during boot instead of using this file.
#
# In contrast to previous versions due to parallel execution during boot
# this script will NOT be run after all other services.
#
# Please note that you must run 'chmod +x /etc/rc.d/rc.local' to ensure——————————————这边让你给他添加x权限，直接执行就行
# that this script will be executed during boot.

touch /var/lock/subsys/local
mount /dev/sr0  /mnt ————————————————————————————————————————————————————————————————添加这行

# 给文件添加可执行权限
[root@rhcsa ~]# chmod +x  /etc/rc.d/rc.local
[root@rhcsa ~]# ll  /etc/rc.d/rc.local
-rwxr-xr-x. 1 root root 494  6月22日 09:39 /etc/rc.d/rc.local

# 重启测试
[root@rhcsa ~]# reboot
```





#### 网络源



*   Redhat10暂时无公开的网络源，可以使用Centos10的yum源

*   在/etc/yum.repos.d 目录下创建.repo结尾的配置文件

*   重建缓存生效

```
[root@rhcsa ~]# vim /etc/yum.repos.d/ali.repo
————————————————————————————————————————————————————————————————————————————————————————————
[ali-BaseOS]
name=ali-BaseOS
baseurl=https://mirrors.aliyun.com/rockylinux/10/BaseOS/x86_64/os/
gpgcheck=0

[ali-AppStream]
name=ali-AppStream
baseurl=https://mirrors.aliyun.com/rockylinux/10/AppStream/x86_64/os/
gpgcheck=0
————————————————————————————————————————————————————————————————————————————————————————————

[root@rhcsa ~]# dnf clean all

[root@rhcsa ~]# dnf makecache

[root@rhcsa ~]# dnf install nginx -y


[root@rhcsa ~]# rpm -qa | grep nginx
nginx-filesystem-1.20.1-26.el9.noarch
nginx-core-1.20.1-26.el9.x86_64
nginx-1.20.1-26.el9.x86_64

[root@rhcsa ~]# rpm -ql nginx-core | grep bin
/usr/sbin/nginx

# 启动服务
[root@rhcsa ~]# /usr/sbin/nginx
```

顺便一提，下载了nginx就相当于是个web服务器了，别人可以通过访问你的IP地址来访问网页







#### EPEL拓展源

定位：官方 BaseOS/AppStream 源里没有的软件，很多都能在 EPEL 里找到

```
[root@rhcsa ~]# vim /etc/yum.repos.d/epel.repo
[epel]
name=epel
baseurl=https://mirrors.aliyun.com/epel/10/Everything/x86_64/
gpgcheck=0

[root@rhcsa ~]# yum clean all

[root@rhcsa ~]# yum makecache
```





#### DNF命令



查看信息

```
# 查看系统中 dnf 仓库配置信息
[root@rhcsa ~]# dnf repolist [all]

# 列出仓库里所有可安装的软件包
[root@rhcsa ~]# dnf list all

# 列出已经安装的所有软件包或指定的 rpm 包(包名中可使用匹配符)
[root@rhcsa ~]# dnf list installed [包名]

# 查看指定软件包的详细信息
[root@rhcsa ~]# dnf info 包名

# 查看仓库里所有包组
[root@rhcsa ~]# dnf grouplist

# 查看指定包组里面的详细信息
[root@rhcsa ~]# dnf groupinfo 包组名
```



安装

```
# 安装指定的软件包，-y 则在工作过程中不需要使用者响应
[root@rhcsa ~]# dnf install [-y] 包名

# 安装指定软件包组
[root@rhcsa ~]# dnf groupinstall [-y] 包组名
```



 升级

```
# 升级指定软件包
# 不加包名则代表，升级主机中所有已安装的软件包
[root@rhcsa ~]# dnf update [-y] 包名

# 升级指定软件包组
[root@rhcsa ~]# dnf groupupdate [-y] 包组名
```



卸载

```
# 卸载已经安装在系统中的指定的软件包
[root@rhcsa ~]# dnf remove [-y] 包名
```



搜索文件由哪个包产生

```
# 查找指定的文件属于哪个包
# 查看某个命令由哪个软件包产生
[root@rhcsa ~]# dnf provides 文件名

[root@rhcsa ~]# dnf whatprovides 文件名
```



搜索软件包（模糊搜索）

```
# 模糊搜索包名
[root@rhcsa ~]# dnf search 模糊包名
```



缓存

```
# 清除 yum 的所有缓存
[root@rhcsa ~]# dnf clean all

# 生成并更新 yum 缓存
[root@rhcsa ~]# dnf makecache
```







### 源码



优势：

​	可以自定义软件功能

​	自定义更安全



源码概念：

​	源码文件：程序编写者使用 C 或 C++ 等语言编写的原始代码文本文件

​	源码文件使用 .tar.gz 或 .tar.bz2 打包成压缩文件

源码特点：

​	源码包可移植性好，与待安装软件的工作环境依赖性不大

​	由于有编译过程，其代码运行效率较高，是针对当前使用者环境的“量体裁衣”

​	安装功能可以定制，可以选择需要的功能进行安装

​	卸载方便，只需删除对应安装目录即可，没有 Windows“注册表”的概念

​	安装过程较为麻烦

安装过程：

​	下载解压缩源码压缩包

​	分析安装软件环境

​	编译软件

​	安装软件





```
# 下载 apache 源码包，并解压，进入 httpd 目录
[root@rhcsa ~]# wget  https://dlcdn.apache.org/httpd/httpd-2.4.57.tar.bz2
[root@rhcsa ~]# tar xf httpd-2.4.57.tar.bz2
[root@rhcsa ~]# cd httpd-2.4.57/

# 环境检测：检测安装环境所需库文件、编译器及编译参数检查
[root@rhcsa httpd-2.4.57]# ./configure
[root@rhcsa httpd-2.4.57]# yum install apr* -y
[root@rhcsa httpd-2.4.57]# ./configure
[root@rhcsa httpd-2.4.57]# yum install pcre* -y
[root@rhcsa httpd-2.4.57]# ./configure

# configure 环境检测通过后生成 Makefile 文件
# 编译 Makefile 文件中记录的信息，生成二进制安装文件
[root@rhcsa httpd-2.4.57]# make
# 提示缺少此文件，则找此文件基于的安装包
[root@rhcsa ~]# yum whatprovides /usr/lib/rpm/redhat/redhat-hardened-ld
# 下载安装包
[root@rhcsa httpd-2.4.57]# yum install redhat-rpm-config -y
[root@rhcsa httpd-2.4.57]# make

# make 生成的二进制安装文件，进行软件安装，默认安装目录 /usr/local 或 /usr/local/bin
[root@rhcsa apache2]# make install

```



```
# nginx 和 httpd 冲突，只能开启一个，关闭掉 nginx 才能启动 httpd
[root@xixi ~]# systemctl stop nginx

# 启动 httpd
[root@xixi ~]# /usr/local/apache2/bin/httpd
```



# 管理Linux进程



概念：

-   **程序**： 是存储在磁盘（如 /usr/bin/ 目录下）的二进制文件，是静态的指令集合，未运行时不占用系统资源。
-   **进程**： 是已启动的可执行程序的运行实例，是动态概念。相同程序基于不同数据集运行会产生不同进程，进程与程序并非一一对应。进程是系统进行资源分配和调度的基本单位。
-   **线程**： 是操作系统运算调度CPU的最小单位，包含于进程，是进程实际运作单元。一个进程可并发多个线程，各线程并行执行不同任务，一个线程仅能属于一个进程。线程虽有程序入口、执行序列和出口，但不能单独执行，需组成进程才能被执行。
-   **总结关系**： 程序是进程的基础，进程是程序的运行态；线程是进程内的执行单元，依赖进程存在，三者共同构成操作系统程序执行和资源管理的体系。





<img src="C:/Users/xgz24/AppData/Roaming/Typora/typora-user-images/image-20261011230250249.png" alt="image-20261011230250249" style="zoom:67%;" />





## 查看进程





### ps

-   查看系统中进程信息



```
ps  -选项 进程名
```

| 参数 | 功能                                                    |
| :--- | :------------------------------------------------------ |
| -a   | 显示所有用户的进程（不包括无终端进程）                  |
| -u   | 以用户为主的格式显示进程信息（显示用户名等信息）        |
| -x   | 显示没有控制终端的进程（后台进程）                      |
| -e   | 显示所有进程，包括没有控制终端的进程（相当于 -ax = -A） |
| -f   | 做一个更完整的输出                                      |



常用：

```
# 常用命令，查看进程，可结合 grep 过滤想看的进程
[root@rhcsa ~]# ps -ef | grep nginx
UID        PID  PPID  C STIME TTY          TIME CMD
root      2948     1  0 09:18 ?        00:00:00 nginx: master process /usr/sbin/nginx
nginx     2949  2948  0 09:18 ?        00:00:00 nginx: worker process
nginx     2950  2948  0 09:18 ?        00:00:00 nginx: worker process

说明：
UID：  运行进程的用户 ID / 用户名
PID：  进程唯一编号
PPID： 父进程编号
C：    进程 CPU 使用率
STIME：进程启动时间
TTY：  所属终端，？代表后台守护进程
TIME： 进程累计占用 CPU 总时长
CMD：  启动进程的命令

[root@rhcsa ~]# ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.9 172420 16756 ?        Ss   09:12   0:01

说明：
USER:    运行进程的用户
PID:     进程ID
%CPU:    CPU占用率
%MEM:    内存占用率
VSZ:     占用虚拟内存
RSS:     占用实际内存 驻留内存
TTY:     进程运行的终端
STAT:    进程状态    man ps (/STATE)
         R          运行
         S          可中断睡眠 Sleep
         D          不可中断睡眠
         T          停止的进程
         Z          僵尸进程
         I          空闲内核进程
         X          死掉的进程

             s          进程的领导者，父进程
             <          <优先级较高的进程
             N          低优先级的进程
             +          前台进程组
             l          多线程进程

START:   进程的启动时间
TIME:    进程占用CPU的总时间
COMMAND: 进程文件，进程名

```





```
# 查看当前用户进程
[root@rhcsa ~]# ps -u
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root      1277  0.0  0.3 224228  5788 pts/0    Ss   09:12   0:00 -bash
root     13949  0.0  0.2 225480  3768 pts/0    R+   09:45   0:00 ps -u

# 自定义显示字段
[root@rhcsa ~]# ps axo user,pid,ppid,%mem,command
USER       PID   PPID %MEM COMMAND
root         1      0  0.5 /usr/lib/systemd/systemd rhgb --system --deserialize 40
root         2      0  0.0 [kthreadd]

# 查看进程树
[root@rhcsa ~]# pstree

[root@rhcsa ~]# ps aux | grep sshd

# 查看指定进程的 PID
[root@rhcsa ~]# pgrep -l sshd
1202 sshd
152950 sshd
153022 sshd
179900 sshd
179904 sshd
179906 sshd
179922 sshd

# 查看父进程
[root@rhcsa ~]# pidof sshd
1202

# 查看指定进程的主进程 PID
[root@rhcsa ~]# cat /run/sshd.pid
1202

```



### top



实时动态监控进程



```
top  -选项
```



| 参数 | 功能                                 |
| :--- | :----------------------------------- |
| -d   | 设置 top 刷新间隔时间（默认 3s）     |
| -i   | 不显示闲置进程（状态为 S 且 %CPU=0） |
| -p   | 指定 PID 的进程                      |

```
[root@rhcsa ~]# top -d 1
[root@rhcsa ~]# top -d 1 -p 1
```





```
[root@rhcsa ~]# top
top - 10:15:19 up  1:02,  1 user,  load average: 0.00, 0.00, 0.00
Tasks: 272 total,   1 running, 271 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.3 us,  0.2 sy,  0.0 ni, 99.2 id,  0.0 wa,  0.2 hi,  0.2 si,  0.0 st
MiB Mem :   1731.7 total,    338.3 free,    897.1 used,    667.6 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.    834.6 avail Mem

上是统计信息区，下是进程信息区

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
   1265 root      20   0   19332   7392   5228 S   0.3   0.4   0:03.77 sshd
      1 root      20   0  172420  16756  10940 S   0.0   0.9   0:01.15 systemd
```



top 的输出分为 统计信息区（前 5 行） 和 进程信息区（5 行后） 两个部分

```shell
统计信息区

# 第一行：系统运行状态 + 平均负载
# top - 10:15:19 up  1:02,  1 user,  load average: 0.00, 0.00, 0.00
10:15:19:当前系统时间
up  1:02:系统运行时长
1 user:登录用户数
load average: 0.00, 0.00, 0.00:系统平均负载（1分钟、5分钟、15分钟）（1表示满载）

# 第二行：进程总数统计
# Tasks: 272 total,   1 running, 271 sleeping,   0 stopped,   0 zombie
272 total:系统总进程数
1 running:运行进程数
271 sleeping:睡眠进程数
0 stopped:暂停进程数
0 zombie:僵尸进程数

# 第三行：CPU 资源占用百分比
# %Cpu(s):  0.3 us,  0.2 sy,  0.0 ni, 99.2 id,  0.0 wa,  0.2 hi,  0.2 si,  0.0 st
0.3 us:用户空间进程占用 CPU 百分比
0.2 sy:内核空间进程占用 CPU 百分比
0.0 ni:调整过谦让值（NI）的进程占用 CPU 百分比
99.2 id:CPU 空闲百分比
0.0 wa:CPU 等待磁盘 IO 的时间百分比
0.2 hi:处理硬件中断的 CPU 时间百分比
0.2 si:处理软件中断的 CPU 时间百分比
0.0 st:被虚拟化技术占用的 CPU 时间百分比

# 第四行：物理内存（Mem）使用情况
# MiB Mem :   1731.7 total,    338.3 free,    897.1 used,    667.6 buff/cache
1731.7 total:物理内存总大小
338.3 free:完全空闲的物理内存大小
897.1 used:已使用的物理内存大小
667.6 buff/cache:内存缓冲区

# 第五行:交换分区（Swap）使用情况
# MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.    834.6 avail Mem
2048.0 total:交换分区总大小
2048.0 free:空闲交换分区大小
0.0 used:已使用的交换分区大小
834.6 avail Mem:可用物理内存大小
```





进程信息区

```
#       PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
#     1265 root      20   0   19332   7392   5228 S   0.3   0.4   0:03.77 sshd
#        1 root      20   0  172420  16756  10940 S   0.0   0.9   0:01.15 systemd
与 ps aux 类似，数据是实时动态更新的

PID:进程唯一 ID
USER:运行进程的用户名
PR:进程优先级
NI:进程谦让值
VIRT:进程占用的虚拟内存大小
RES:进程占用的物理内存大小
SHR:进程占用的共享内存大小
S:进程状态
%CPU:进程实时 CPU 使用率
%MEM:进程实时内存使用率
TIME+:进程累计占用 CPU 时间
COMMAND:进程启动命令

```

注意：可以在 RHEL10 中安装 epel-release 源后安装 htop，htop 是 top 的升级版





## 终止进程

### kill

强制结束进程（kill）





```
kill     -选项     进程ID
killall  -选项     进程名
```



| 参数 | 功能                         |
| :--- | :--------------------------- |
| -9   | 强制终止进程                 |
| -1   | 重启进程                     |
| -15  | 请求进程自动终止             |
| -l   | 列出所有可用的信号名称和编号 |
| -u   | 终止指定用户的所有进程       |

```
[root@rhcsa ~]# ps aux | grep passwd ————————————————————————————————查找passwd相关进程
[root@rhcsa ~]# kill -9 997
```

a          显示所有进程，包括其他用户的进程 

u          以用户为主的格式显示进程详细信息（显示用户名、CPU、内存占用等） 

x          显示没有控制终端的进程（如后台服务、守护进程）

































