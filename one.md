one







文件管理

用户管理

权限管理

网络管理

软件管理

磁盘管理











```
root@192:~# ip address

查看IP地址
```





```
root@192:~# hostnamectl hostname rhcsa

修改主机名为rhcsa
```



```
root@rhcsa:~# reboot

重启电脑
```



```sh
root@rhcsa:~# poweroff 

关机
```









基础课的前面笔记就写的小白简洁一点了，

<img src="C:/Users/xgz24/AppData/Roaming/Typora/typora-user-images/image-20261006171132843.png" alt="image-20261006171132843" style="zoom: 25%;" />

拍摄快照（相当于一个记录点）：

​	推荐关机状态下拍摄优势：速度快

​	开机状态下拍摄：速度慢







|    快捷键    |     作用     |
| :----------: | :----------: |
|     tab      |   单词补全   |
|    ctrl+c    | 终止当前任务 |
|    ctrl+l    |     清屏     |
| ctrl+insert  |     复制     |
| shift+insert |     粘贴     |
| ctrl+shift+= |   放大字号   |
|    ctrl+-    |   缩小字号   |
|    ctrl+z    |   终止进程   |









<img src="C:/Users/xgz24/AppData/Roaming/Typora/typora-user-images/image-20261006202606786.png" alt="image-20261006202606786" style="zoom:50%;" />

 **Linux 内核**：内核是系统的核心，负责运行程序和管理磁盘、打印机等硬件设备。

• **Shell**：Shell 是用户与内核之间的交互接口，分为两种类型：

○ **图形界面 Shell**：如 KDE、GNOME，提供可视化操作环境

○ **字符界面 Shell**：如 bash，通过命令行执行操作

○ **作用**：当用户发出指令（命令或鼠标操作）时，Shell 会先接收并翻译，再传递给内核执行。内核完成硬件操作后，会将结果返回给 Shell，再由 Shell 呈现给用户。

• **应用程序**：指运行在系统之上的各类工具和软件，包括文本编辑器、编程语言、X Window、办公套件、互联网工具、数据库等，用于满足用户的具体业务和使用需求。











Linux有六个不同的终端，Ctrl+Alt+F[1,2,3,4,5,6]，即可转到对应的终端







```text
你是谁、你在哪、干什么

示例 1（root 用户）：
[root@localhost ~]#
 │  │    │      │ └─ 提示符标志：#（root）或 $（普通账户）
 │  │    │      └─── 当前工作目录：~ 表示当前用户的家目录——————————————————————这里的波浪号代表/root
 │  │    └──────────── 主机名：默认为 localhost
 │  └───────────────── 分隔符：@
 └──────────────────── 当前登录的账户名：root（超级账户/管理员）

示例 2（普通用户）：
redhat@rhcsa:~$————————————————————————————————————————————————————————————这里的波浪号代表/home/redhat
xiaoming@rhcsa:~$


说明：
root 家目录: /root/
普通账户 家目录: /home/用户名


【第一个 / ：根目录】
【除了第一个 / 之外的 / ：分隔符】
```





```
查看当前 Linux 发行版本信息
root@rhcsa:~# cat /etc/redhat-release——————————————————cat就是查看当前文件信息，其实就是查看了/etc/redhat-release的信息
Red Hat Enterprise Linux release 10.2 (Coughlan)


查看内核版本
root@rhcsa:~# uname -r
6.12.0-211.7.3.el10_2.x86_64
6 主版本号,12 次版本号,0-124 次要修订版本,8.1 补丁版本,el10_1 发行版标识,x86_64 架构标识，适用于 64 位 x86 处理器

```

```
查看 shell 的类型

查看当前系统支持所有 shell
root@rhcsa:~# cat /etc/shells
/bin/sh
/bin/bash/usr
/bin/sh/usr
/bin/bash


查看当前默认 shell
[root@rhcsa ~]# echo $SHELL
/bin/bash
```









可以通过MobaXterm软件把Windows文件传到Linux，也可以将Linux的文件下载到Windows里面来，前提是得远程操控











