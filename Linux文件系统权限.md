

# Linux文件系统权限



<img src="C:\Users\xgz24\AppData\Roaming\Typora\typora-user-images\image-20261010210551385.png" alt="image-20261010210551385" style="zoom:67%;" />







## 文件权限构成

根据文件归属性对象分为:

​	owner：所有，缩写u

​	group：所属组，缩写g

​	other：其他人，缩写o



访问者三种权限

<img src="C:\Users\xgz24\AppData\Roaming\Typora\typora-user-images\image-20261010211305853.png" alt="image-20261010211305853" style="zoom:80%;" />





| 字符表示 | 二进制表示 | 数字表示 |
| :------: | :--------: | :------: |
|    —     |    000     |    0     |
|    –x    |    001     |    1     |
|   -w-    |    010     |    2     |
|   -wx    |    011     |    3     |
|    r–    |    100     |    4     |
|   r-x    |    101     |    5     |
|   rw-    |    110     |    6     |
|   rwx    |    111     |    7     |



字符型表示需要9个字母，数字型表示需要3个数字





|    权限     | 对文件                                                       | 对目录                                                       |
| :---------: | ------------------------------------------------------------ | ------------------------------------------------------------ |
|  r (read)   | 可以读取文件的内容（cat）                                    | 可以列出目录下的内容，即目录下的文件的文件名（需要与 x 权限连用） |
|  w (write)  | 可以更改文件的内容（>）                                      | 可以创建或者删除目录中的任意文件（只有 w 权限无法创建删除文件，需要和 x 权限一起使用） |
| x (execute) | 可以作为可执行文件，如脚本执行（只有 x 权限不可执行文件，需要与 r 权限连用） | 可以切换到目录（cd）                                         |

注意：

-   root 不受读写权限限制
-   只受执行权限限制

```
[redhat@rhcsa ~]$ cat xgz
r权限

[redhat@rhcsa ~]$ echo lele >> xgz
w权限

[redhat@rhcsa ~]$ ./xgz
x权限
```







## 修改文件目录的所属者和所属组



### chown

-   修改文件或目录的所有者和所属组



```
chown -选项 所有者:所属组 文件名或目录名
```

| 参数 | 功能                                        |
| ---- | ------------------------------------------- |
| -R   | 递归修改目录即所有子文件、子目录的所属者/组 |



```
[root@rhcsa ~]# chown redhat:redhat file1 —————————————————————————————针对文件

[root@rhcsa ~]# chown -R redhat:redhat file1 ——————————————————————————针对目录，里面的文件也跟着改动
```



## 修改文件的权限



### chmod

-   chmod（change mode）：修改文件或目录的权限

```
# 字符型
chmod    -选项    [ugoa] [+-=] [rwx]    文件或目录名...

# 数字型
chmod    -选项    nnn    文件或目录名...
```

| 选项 | 功能                                             |
| ---- | ------------------------------------------------ |
| -R   | 递归修改目录下所有文件，以及子目录下所有文件权限 |



```
[root@rhcsa ~]# chmod o+x file1 ————————————————————————————————将file1的其他人权限加上x

[root@rhcsa ~]# chmod o=w file1 ————————————————————————————————修改file1的其他人权限为-w-（直接覆盖）

[root@rhcsa ~]# chmod 777 file1 ————————————————————————————————修改file1的所有的权限为rwx
```



注意：

​	普通文件默认权限：644

​	目录文件默认权限：755







## 特殊权限

-   Linux 系统中，用户对文件或目录的访问权限除了 rwx 一般权限外，还有

    ​	SET UID（SUID）

    ​	SET GID（SGID）

    ​	StickyBit（粘滞位）

-   用于对文件或目录进行更加灵活方便的访问控制



### SUID

-   可执行文件设置：普通用户执行此文件时临时获得该文件所有者权限

    ​	所有者 u 执行位显示 s（文件本身有 x 权限）或 S（文件本身没有 x 权限）

    ```
    chmod u+s 文件或目录
    设置 SUID
    
    chmod u-s 文件或目录
    取消 SUID
    ```

    

-   普通用户修改自身密码，修改的新密码需要保存到 /etc/shadow 文件中，而 /etc/shadow 文件的权限为 —（只有 root 可修改），那么怎么修改自己的密码呢

    ​	passwd 命令用于修改用户密码，其所属所有者为 root，且设置了 SUID 权限。

    ​	普通用户执行 passwd 时，可获得 root 权限，从而能够修改 /etc/shadow 文件中的密码信息。

```
[root@rhcsa ~]# ll /usr/bin/passwd
-rwsr-xr-x. 1 root root 32648 Aug 10  2021 /usr/bin/passwd
[root@rhcsa ~]# ll /etc/shadow
---------- 1 root root 1140 Jan  2 18:03 /etc/shadow
```

注意：

-   suid 仅对二进制文件有效
-   在执行过程中，调用者会暂时获得该文件的所有者权限
-   该权限只在程序执行的过程中有效





### SGID



-   目录设置：在此目录下新创建的文件和子目录继承其组属性（所属组为该目录所属组）（团队协作）

    ​	就是给目录设置 SGID 后，任何人在这个目录里新建的文件/子目录，其所属组都会自动变成该目录的所属组，而不是创建者自己的主组。

-   可执行文件设置：普通用户执行此文件时临时获得文件所属组权限

    ​	所属组 g 执行位显示 s（文件/目录本身有 x 权限，必须要有x才能生效）或 S（文件/目录本身没有 x 权限，没有生效）

    ```
    chmod g+s 文件或目录
    设置 SGID
    
    chmod g-s 文件或目录
    取消 SGID
    ```

    

注意：

-   一般 SGID 多用在特定的多人团队的项目开发上，在系统中用的很少

```
# 实验准备
[root@rhcsa ~]# mkdir /test
[root@rhcsa ~]# ll -d /test
drwxr-xr-x. 2 root root 6 6月 8日 18:18 /test
[root@rhcsa ~]# groupadd kaifa
[root@rhcsa ~]# chown root:kaifa /test
# 该目录所属组修改为了 kaifa
[root@rhcsa ~]# ll -d /test
drwxr-xr-x. 2 root kaifa 6 6月 8日 18:18 /test

# 在该目录没有 SGID 的权限下，创建文件，文件所属组不会继承父目录的所属组
[root@rhcsa ~]# touch /test/rootfile1
[root@rhcsa ~]# ll /test/
总用量 0
-rw-r--r--. 1 root root 0 6月 8日 18:20 rootfile1
```



###  Sticky Bit 粘滞位

-   目录设置：公共可写目录，在该目录下，用户只能“删除/重命名/移动”自己创建的文件（root 不受限制）

    ​	其他人 o 执行位显示 t（目录本身有 x 权限）或 T（目录本身没有 x 权限）

    ```
    设置 Sticky Bit
    chmod o+t 目录
    
    取消 Sticky Bit
    chmod o-t 目录
    ```
    
    

```
# 上述公共目录里的文件，所有人都可以删除，不安全
[redhat@rhcsa ~]$ ll /test
-rw-r--r--. 1 root   root  0 6月 8日 18:20 rootfile1


# redhat 用户可以把 root 的文件删除
[redhat@rhcsa ~]$ rm -rf /test/rootfile1


# 给其他人这一组添加上 t 的权限（使用 root 账户添加）
[root@rhcsa ~]# ll -d /test
drwxr-srwx. 2 root kaifa 42 6月 8日 18:27 /test
[root@rhcsa ~]# chmod o+t /test
[root@rhcsa ~]# ll -d /test
drwxr-srwt. 2 root kaifa 42 6月 8日 18:27 /test

[redhat@rhcsa ~]$ rm -rf /test/rootfile2    # 不允许删除其他用户的文件
rm: 无法删除 '/test/rootfile2': 不允许的操作
```





### 设置特殊权限

-   字符格式：

    ​	SUID：chmod u±s

    ​	SGID：chmod g±s

    ​	Sticky Bit：chmod o±t

-   数字格式：chmod nnnn

    ​	后三位是一般权限，第一位是特殊权限（第一位标志数字如下）



|      特殊权限      | 二进制表示 | 数字表示 |
| :----------------: | ---------- | -------- |
|         -          | 000        | 0        |
|       Sticky       | 001        | 1        |
|        SGID        | 010        | 2        |
|    SGID、Sticky    | 011        | 3        |
|        SUID        | 100        | 4        |
|    SUID、Sticky    | 101        | 5        |
|     SUID、SGID     | 110        | 6        |
| SUID、SGID、Sticky | 111        | 7        |







## ACL权限

-   给指定用户，指定目录分配指定权限（更细化的权限设置）
-   上述权限的设置都是针对所属者所属组其他人，这三个大类的限制，如果要对其他人中的某一个人进行限制，就要用到 ACL 访问控制列表来更加精确的限制某个人的权限

### getfacl

-   查看 ACL 权限

```
getfacl    文件名
[root@rhcsa ~]# touch temp.cfg
[root@rhcsa ~]# getfacl temp.cfg        # 默认无 ACL 权限
# file: temp.cfg
# owner: root
# group: root
user::rw-
group::r--
other::r--
```







### setfacl

-   设置 ACL 权限

    可以精确到"某个用户"或"某个组"单独设置权限，是传统权限的扩展和补充

```
setfacl    -选项    文件名
```

| 参数 | 功能                                                         |
| :--: | ------------------------------------------------------------ |
|  -m  | 设置 ACL 条目<br />给用户设置 ACL 权限：setfacl -m u:用户名:权限 文件名<br />给用户组设置 ACL 权限：setfacl -m g:组名：权限 文件名 |
|  -x  | 删除指定 ACL 条目                                            |
|  -b  | 删除所有 ACL 条目                                            |
|  -d  | 设置目录默认 ACL 条目（针对目录） <br />（在此目录下新创建的文件和子目录会继承该默认 ACL） |
|  -k  | 删除目录默认 ACL 权限                                        |
|  -R  | 递归设置 ACL 权限                                            |

-   例：root 用户在根目录下创建目录 /project 及所属工作组 QQgroup
-   所属组里面创建两个用户 zhangsan 和 lisi，此文件权限是 770
-   再创建一个旁听用户 pt，给他设定 /project 目录的 ACL 为 r-x



```
[rootarhcsa ~]# setfac1 -m u:pt:rx /project/
给用户pt对于/project/添加了rx权限

[root@rhcsa ~]# setfacl -x u:pt /project/
删除pt的ACL权限
```



<img src="C:/Users/xgz24/AppData/Roaming/Typora/typora-user-images/image-20261011114012311.png" alt="image-20261011114012311" style="zoom:67%;" />

有ACL的话就会在权限后加上+



## 权限掩码



### umask

-   显示或者设置文件和目录的默认权限

-   在 Linux 系统中，当用户创建一个新的文件或目录时，系统都会为新建的文件或目录分配默认的权限，该默认权限与 umask 值有关

    文件：实际默认权限 = 理论默认权限（0666）- umask 值

    目录：实际默认权限 = 理论默认权限（0777）- umask 值

我们常说的644，755省略了前面的0，应该是0644和0755



```
[root@rhcsa ~]# umask 000
# 临时修改 umask 值

# 永久修改 umask 值
[root@rhcsa ~]# vim /etc/profile
umask 0000                        # 在文件最后添加此行内容
[root@rhcsa ~]# source /etc/profile
# 方法一

[root@rhcsa ~]# vim ~/.bashrc
umask 0000
[root@rhcsa ~]# source ~/.bashrc
# 方法二
```



umask：0077，文件：0666-0077=600，目录：0777-0077=700（每一位单独减，减光了没关系）

umask：0033，文件：0666-0033=644，目录：0777-0033=744

​	rw- rw- rw-                                        rwx rwx rwx

​	—   -wx  -wx                                      —   -wx  -wx

​	rw- r–    r–                                         rwx r–    r–

（-减去x当然还是等于-）



**小技巧：**

目录正常加减法就行了

普通文件：如果umask是奇数，则需要将umask每一位都减一，再做加减法







