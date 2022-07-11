### 2、linux关机&重启命令

>`注意：`在关机或者重启(`shutdown或reboot`)前要先执行`sync`指令

| shutdown        |                         |
| --------------- | ----------------------- |
| shutdown -h now | 表示立即关机            |
| shutdown -h 1   | 表示1分钟后关机         |
| shutdown -r now | 立即重启                |
| halt            | 直接使用效果等价于关机  |
| reboot          | 就是重启系统            |
| sync            | 把内存的数据同步到磁盘. |

### 3、用户管理

#### 3.1、添加用户（`useradd`）

> useradd [可选项] 用户名

##### 3.1.1、细节说明

- 当创建用户成功后，会自动的创建和用户同名的家目录
- 也可以通过`useradd -d`指定目录，新的用户名，给新创建的用户指定家目录

#### 3.2、给用户指定密码（`passwd`）

>passwd  用户名

#### 3.3、删除用户（`userdel`）

>userdel  用户名

##### 3.3.1、删除用户，但是要保留家目录

>userdel  用户名

##### 3.3.2、删除用户以及用户主目录

> userdel  -r  用户名

#### 3.3、查询用户信息（`id`）

> id  用户名

#### 3.4、查看当前登录用户（`whoami`）

> whoami

#### 3.5、用户组

##### 3.5.1、增加用户组（`groupadd`）

> groupadd  组名

##### 3.5.2、删除用户组（`groupdel`）

> groupdel  组名

##### 3.5.3、增加用户时直接加上组

> useradd -g 用户组  用户名

##### 3.5.4、修改用户的组

> usermod -g 用户组  用户名

3.5.5、用户和组的配置文件的位置

- 用户配置文件`/etc/passwd`

  每行的含义：用户名:口令(密码加密):用户标识号:组标识号:注释性描述:主目录:登录Shell

- 组配置文件`/etc/group`

  每行含义：组名:口令:组标识号:组内用户列表

- 口令配置文件(密码和登录信息)`/etc/shadow`

### 4、linux实用指令

#### 4.1、linux运行级别

> 切换运行级别指令：`init [0,1,2,3,5,6]`

| 0    | 关机                     |
| ---- | ------------------------ |
| 1    | 单用户【找回丢失密码】   |
| 2    | 多用户状态`没有网络服务` |
| 3    | 多用户状态`有网络服务`   |
| 4    | 系统未使用保留给用户     |
| 5    | 图形界面                 |
| 6    | 系统重启                 |

常用运行级别是3和5，要修改默认的运行级别可改文件`/etc/inittab `的 `id:5:initdefault:`这一行中的数字

##### 4.1.1、运行级别找回`root密码`

>思路：进入到单用户模式，然后修改root密码。

#### 4.2、帮助相关的指令

##### 4.2.1、man获得帮助信息

> man  [指令或配置文件]

##### 4.2.2、help获得帮助信息

> help [指令或配置文件]

#### 4.3、文件目录类的指令

##### 4.3.1、pwd指令

>pwd  (功能描述：显示当前`工作目录的绝对路径`)

##### 4.3.2、ls指令

>`ls  [选项]  [文件或目录]`
>
>1. `-a `：显示当前目录所有的文件和目录，包括隐藏的。
>2. `-l`：以列表的方式显示信息、

##### 4.3.3、cd指令

> `cd  [参数]`  （功能描述:切换到指定目录）
>
> 1. `cd~`  或者 ` cd` 回到自己的家目录
> 2. `cd ..  `回到当前目录的上一级目录
> 3. 绝对路径 : /home 即从根目录开始定位。
> 4. 相对路径 : ../home, 从当前工作目录开始定位到需要的目录去

##### 4.3.4、mkdir指令

>`mkdir  [选项]  目录名`  （用于创建目录）
>
>1. `-p`：创建多级目录

##### 4.3.5、rmdir指令

> `rmdir  [选项]  要删除的空目录`  (用于删除空目录)
>
> 1. 只能删除空目录

##### 4.3.6、touch指令

> `touch  文件名`  （用于创建空文件）
>
> 1. 可一次性创建多个文件

##### 4.3.7、cp指令

>`cp  [选项]  要复制文件的位置  复制到那个文件下`
>
>1. `-r`：递归复制整个文件夹
>2. `\cp  [选项]  要复制文件的位置  复制到那个文件下` ：这个指令会强制覆盖

##### 4.3.8、rm指令

> `rm  [选项]  要删除的文件或目录`  （用于移除文件或目录）
>
> 1. `-r`：递归册]除整个文件夹
> 2. `-f `：强制删除不提示

##### 4.3.9、mv指令

> `mv  oldNameFile  newNameFile`  （重命名文件）
>
> `mv  /movedFolder  /targetFolder`（移动文件）

##### 4.3.10、cat指令

> `cat  [选项]  要查看的文件  |  more` （查看文件内容有，以只读的方式打开）
>
> 1. `-n` ：显示行号
> 2. cat只能浏览文件，而不能修改文件，为了浏览方便，一般会带上管道命令`| more`

##### 4.3.11、more指令

> more指令是一个基于Vi编辑器的文本过滤器，它以全屏幕的方式按页显示文本文件的内容。
>
> `more  要查看的文件名`

| 操作            | 功能说明                             |
| --------------- | ------------------------------------ |
| `空白键(space)` | 代表向下翻`一页`                     |
| `Enter`         | 代表向下翻`一行`                     |
| `q`             | 代表立刻离开more，不再显示该文件内容 |
| `Ctrl+F`        | 向下滚动一屏                         |
| `Ctrl+B`        | 返回上一屏                           |
| `=`             | 输出当前行的行号                     |
| `:f`            | 输出文件名和当前行的行号             |

##### 4.3.12、less指令

> less指令`用来分屏查看文件内容`，它的功能与more指令类似，但是比more指令更加强大，支持各种显示终端。less指令在显示文件内容时，并不是一次将整个文件加载之后才显示，`而是根据显示需要加载内容，对于显示大型文件具有较高的效率`。
>
> `less  要查看的文件`

| 操作            | 功能说明                                             |
| --------------- | ---------------------------------------------------- |
| `空白键(space)` | 代表向下翻`一页`                                     |
| `pageup`        | 代表向上翻动一页                                     |
| `/字串`         | 向下搜导「字串』的功能:（n：向下查找；N：向上查找）; |
| `?字串`         | 向上搜导「字串』的功能:（n：向上查找；N：向下查找）  |
| `q`             | 离开less这个程序                                     |

##### 4.3.13、`>`指令和`>>`指令

> `>`输出重定向（`会将原来的文件的内容覆盖`）和`>>`追加（不会覆盖原来文件的内容，而是`追加到文件的尾部`。）
>
> 1) `ls -l  >  文件` (功能描述:列表的内容`写入文件中`（`覆盖写`))
> 2) `ls -al   >>  文件`(功能描述:列表的内容`追加到文件的末尾`)

##### 4.3.14、echo指令

> `echo  [选项]  [输出内容]` （输出内容到控制台）

##### 4.3.15、head指令

> `head  文件` (功能描述：查看文件头10行内容)
>
> `head -n 5 文件` （功能描述：查看文件头5行内容，`5可以是任意行数`）

##### 4.3.16、tail指令

>`tail  文件`(功能描述：查看文件后10行内容)
>
>`tail  -n  5  文件`(功能描述：查看文件后5行内容，`5可以是任意行数`)
>
>`tail  -f  文件`  (功能描述：实时追踪该文档的所有更新)

##### 4.3.17、In指令

> 软链接也叫符号链接，类似于`windows里的快捷方式`，主要存放了链接其他文件的路径
>
> `ln  -s  [原文件或目录]  [软链接名] `  (功能描述:给原文件创建一个软链接)

##### 4.3.18、history指令

>`history`  (功能描述：查看已经执行过历史命令)

#### 4.4、时间日期类指令

>1. `date`  (功能描述：显示当前时间)
>2. `date +%Y ` (功能描述：显示当前年份)
>3. `date +%m`  (功能描述：显示当前月份)
>4. `date +%d` (功能描述：显示当前是哪一天)
>5. `date "+%Y-%m-%d %H:%M:%S"`(功能描述：显示年月日时分秒)
>6. `date  -s  字符串时间`  (设置日期)
>7. `cal [选项] ` (功能描述：不加选项，显示本月日历)

#### 4.5、搜索查找类指令

##### 4.5.1、find指令

> find指令将从指定目录向下递归地遍历其各个子目录，将满足条件的`文件或者目录`显示在终端。
>
> `find  [搜索范围(从哪个目录下开始查找)]  [选项]  要查找文件名`
>
> 1. `-name<查询方式>`：按照指定的文件名查找模式查找文件
>
>    find /root -name hello.txt
>
> 2. `-user<用户名>`：查找属于指定用户名所有文件
>
>    find /opt -user root
>
> 3. `-size<文件大小>`：按照指定的文件大小查找文件.`(+n：大于；-n：小于；n：等于)`
>
>    find  /root  -size  +20M 

##### 4.5.2、locate指令

>locate指令可以快速定位文件路径。locate指令利用事先建立的系统中所有`文件名称及路径的locate数据库`实现快速定位给定的文件。Locate指令无需遍历整个文件系统，查询速度较快。为了保证查询结果的准确度，管理员必须定期更新locate时刻。
>
>`locate  搜索文件名`
>
>`特别说明`：由于locate 指令基于数据库进行查询，所以第一次运行前，必须使用`updatedb指令创建locate数据库`。

##### 4.5.3、`grep`指令和管道符 `|`

> grep过滤查找，管道符，"|"，表示将前一个命令的处理结果输出传递给后面的命令处理。
>
> `grep  [选项]  查找内容  源文件`
>
> cat  hello.txt | grep -n yes
>
> 1. `-n`：显示匹配行及行号
> 2. `-i`：忽略字母大小写

#### 4.6、压缩和解压类

##### 4.6.1、gzip / gunzip指令

> gizp用于压缩文件，gunzip用于解压文件
>
> `gzip  文件`（功能描述：压缩文件，只能将文件压缩为*.gz文件)
>
> ​	`细节说明`：当我们使用gzip对文件进行压缩后，不会保留原来的文件。
>
> `gunzip  文件.gz`  (功能描述：解压缩文件命令)

##### 4.6.2、zip / unzip指令

> zip 用于压缩文件，unzip 用于解压的，这个在项目打包发布中很有用的
>
> `zip  [选项]  XXX.zip  将要压缩的内容`  (功能描述：压缩文件和目录的命令)
>
> 1. `-r`：递归压缩，及压缩目录
>
> `unzip  [选项]  XXX.zip`  (功能描述：解压缩文件)
>
> ​	unzip -d /opt/temp/ mypackage.zip
>
> 1. `-d<目录>`：指定解压后文件的存放目录

##### 4.6.3、tar指令

> tar指令是打包指令，最后打包后的文件是.tar.gz的文件。
>
> `tar  [选项]  XXX.tar.gz  打包的内容`  (功能描述：打包目录，压缩后的文件格式.tar.gz)
>
> 压缩：`-zcvf`
>
> ​		tar -zcvf a.tar.gz  a.txt b.txt
>
> ​		tar -zcvf myhome.tar.gz /home/
>
> 解压：`-zxvf`
>
> ​		tar -zxvf a.tar.gz
>
> ​		tar -zxvf myhome.tar.gz -C /opt/temp/【指定解压的目录事先要有】
>
> 1. `-c·`：产生.tar打包文件
> 2. `-v`：显示详细信息
> 3. `-f`：指定压缩后的文件名
> 4. `-z`：打包同时压结
> 5. `-x`：解包.tar文件

### 5、linux实操篇，组管理和权限管理

#### 5.1、linux组的基本介绍

> 在linux中的每个用户必须属于一个组，不能独立于组外。在linux中每个文件有所有者、所在组、其它组的概念。

##### 5.1.1、查看文件的所有者和（文件目录）所在组（`ls`）

> `ls  -ahl`  
>
> 应用实例：创建一个组police,再创建一个用户tom,将tom 放在police组―然后使用tom 来创建一个文件ok.txt，看看情况如何。

![image-20220109104844464](C:\Users\86157\AppData\Roaming\Typora\typora-user-images\image-20220109104844464.png)

![image-20220109104916300](C:\Users\86157\AppData\Roaming\Typora\typora-user-images\image-20220109104916300.png)

##### 5.1.2、修改文件的所有者（`chown`）

> `chown  [选项]  用户名  文件名`
>
> `chown  [选项]   newowner  file ` （改变文件的所有者）
>
> `chown  [选项]  newowner:newgroup  file`  （改变用户的所有者和所有组）
>
> 1. `-R`： 如果是目录则使其下`所有子文件或目录递归生效`）

![image-20220109110454277](C:\Users\86157\AppData\Roaming\Typora\typora-user-images\image-20220109110454277.png)

##### 5.1.3、修改文件所在的组（`chgrp`）

> `chgrp  [选项]  组名  文件名`
>
> 1. `-R`： 如果是目录则使其下`所有子文件或目录递归生效`）

##### 5.1.4、改变用户所在组（`usermod`）

>`usermod  -g  组名  用户名`
>`usermod  -d  目录名  用户名`  （改变该用户登陆的初始目录）

#### 5.2、权限的基本介绍

> `-rw-r--r--. 1 tom police 0 1月   9 10:46 ok.txt`
>
> 1. 第0位`-`确定文件的类型
>    - 文件的类型：
>      1. `-`：普通文件
>      2. `d`：目录
>      3. `1`：软链接
>      4. `c`：字符设备【键盘，鼠标】
>      5. `h`：快文件，硬盘
> 2. 第1-3位`rw-`确定`所有者（该文件的所有者)`拥有该文件的权限。---User
> 3. 第4-6位`r--`确定`所属组（同用户组的）`拥有该文件的权限，---Group
> 4. 第7-9位`r--`确定`其他用户`拥有该文件的权限---Other
> 5. 第10位：如果是文件，表示硬链接的数，如果是目录则表示`该目录的子目录数`

##### 5.2.1、rwx权限详解

>`rwx作用到文件`
>
>1. [ r ]代表可读(read)：可以读取,查看
>2. [ w]代表可写(write)：可以修改，但是不代表可以删除该文件，删除一个文件的前提条件是对该文件所在的目录有写权限，才能删除该文件。
>3. [x]代表可执行(execute)：可以被执行

>`rwx作用到目录`
>
>1. [ r ]代表可读(read)：可以读取，ls查看目录内容
>2. [ w]代表可写(write)：可以修改，目录内创建+删除+重命名目录
>3. [ x]代表可执行(execute)：可以进入该目录

##### 5.2.2、修改文件和目录权限——chmod指令（`chmod`）

>#### 第一种方式:+、-、=变更权限
>
>`u`：所有者  `g`：所有组  `o`：其他人  `a`：所有人(u、g、o的总和)
>
>1.  `chmod  u=rwx,g=rx,o=x  文件目录名`（给文件的所有者添加权限，给所在组添加权限，给其它组添加权限。）
>
>2) `chmod  o+w  文件目录名`  （给文件的`其他用户追加权限`）
>3) `chnlod  a-x  文件目录名`  （给文件的`所有用户`添加权限）

>#### 第二种方式:通过数字变更权限
>
>r = 4，w = 2，x = 1，rwx = 4 + 2 + 1 = 7
>
>`chmod u=rwx,  g=rx,  o=x  文件目录名`相当于  `chmod  751  文件目录名`

#### 5.3、crond任务调度（`crond`）

>任务调度：是指系统在`某个时间执行的特定的命令或程序`。
>
>任务调度分类:
>
>1. 系统工作:有些重要的工作必须周而复始地执行。如病毒扫描等
>
>2. 个别用户工作:个别用户可能希望执行某些程序，比如对mysq1数据库的备份。
>
>   `crontab  [选项]`
>
>   1. `-e`：编辑crontab定时任务
>   2. `-l`：查询crontab任务
>   3. `-r`：删除当前用户所有的crontab任务

五个占位符说明`*/1 * * * * ls -l /etc/ > /temp/to.txt `

![image-20220110211447270](C:\Users\86157\AppData\Roaming\Typora\typora-user-images\image-20220110211447270.png)

![image-20220110211648555](C:\Users\86157\AppData\Roaming\Typora\typora-user-images\image-20220110211648555.png)

![image-20220110211750088](C:\Users\86157\AppData\Roaming\Typora\typora-user-images\image-20220110211750088.png)

#### 5.4、磁盘分区和挂载

##### 5.4.1、查看系统的分区和挂载情况（`lsblk`）

> `lsblk -f`

##### 5.4.2、虚拟机增加硬盘步骤

>1. 虚拟机添加硬盘
>
>2. 分区  `fdisk   /dev/sdb`
>
>3. 格式化 `mkfs  -t  ext4  /dev/sdb1`
>
>4. 挂载  先创建一个  /home/newdisk , 挂载` mount  /dev/sdb1   home/newdisk`
>
>5. 设置可以自动挂载 (永久挂载，当你重启系统，仍然可以挂载到/home/newdisk)。
>
>   永久挂载：通过修改`/etc/fstab`文件实现挂载添加完成后执行`mount -a`即刻生效

##### 5.4.3、查询系统整体磁盘使用情况（`df`）

> `df  -h`

##### 5.4.4、查询指定目录的磁盘占用情况（`du`）

> `du  -h  /目录`
>
> 1. `-s`：指定目录占用大小汇总
> 2. `-h`：带计量单位
> 3. `-a`：含文件
> 4. `--max-depth=1`：子目录深度
> 5. `-c`：列出明细的同时，增加汇总值

>1. 统计/home文件夹下文件的个数  ` ls -l /home | grep "^-" | wc -l`
>2. 统计/home文件夹下目录的个数  ` ls -l /home | grep "^d" | wc -l`
>3. 统计/home文件夹下文件的个数，包括子文件夹里的  ` ls -lR /home | grep "^-" | wc -l`
>4. 统计文件夹下目录的个数，包括子文件夹里的  ` ls -lR /home | grep "^d" | wc -l`
>5. 以树状显示目录结构  `tree`

#### 5.5、网络配置

#### 5.6、进程管理

##### 5.6.1、显示系统执行的进程（`ps`）

> `ps  -a`：显示当前终端的所有进程信息
>
> `ps  -u`：以用户的格式显示进程信息
>
> `ps  -x`：显示后台进程运行的参数

| 字段 | 说明                   |
| ---- | ---------------------- |
| PID  | 进程识别号             |
| TTY  | 终端机号               |
| TIME | 此进程所消CPU 时间     |
| CMD  | 正在执行的命令或进程名 |

`应用实例`

以全格式显示当前所有的进程，查看进程的父进程。

`ps -ef`  是以全格式显示当前所有的进程
`-e`显示所有进程。`-f`全格式。

##### 5.6.2、终止进程（`kill或killall`）

>`kill  [选项]  进程号`（功能描述:通过进程号杀死进程)
>`killall  进程名称`（功能描述:通过进程名称杀死进程，也支持通配符，这在系统因负载过大而变得很慢时很有用)
>
>1. `-9`：表示强迫进程立即停止

##### 5.6.3、查看进程树（`pstree`）

> `pstree  [选项]`,可以更加直观的来看进程信息
>
> 1. `-p` ：显示进程的PID
> 2. `-u` ：示进程的所属用户

#### 5.7、服务管理

> `systemctl  服务名  [start | stop | restart | reload | status]`
>
> 1. 关团或者启用防火墙后，立即生效。[`telnet  测试某个端口即可`]
> 2. 这种方式只是临时生效，当重启系统后，还是回归以前对服务的设置。

##### 5.7.1、查看服务名

> 使用setup ->系统服务就可以看到
>
> /etc/init.d/服务名称

##### 5.7.2、动态监控进程（`top`）

> top与ps命令很相似。它们都用来显示正在执行的进程。Top与ps最大的不同之处，在于top在执行一段时间可以更新正在运行的的进程。
>
> `top [选项]`
>
> 1. `-d 秒数`：指定top命令每隔几秒更新．默认是3秒在top命令的交互模式当中可以执行的命会
>
> 2. `-i`：使top不显示任何闹益或者僵死进程
>
> 3. `-p`：通过指定监控进程ID来仅仅监挖某个进程的状态.
>
>    交互指令
>    p：以CPU使用率排序,默认就是此项
>    M：以内存的使用率排序
>    N：以PID排序
>    q：退出top

##### 5.7.3、查看系统网络情况和端口（`netstat`和`firewall`）

> `netstat  [选项]`
>
> 1. `-an`：按一定顺序排列输出
> 2. `-p`：显示那个进程在调用

>- - 查看防火墙某个端口是否开放
>
>- `firewall-cmd --query-port=80/tcp`
>
>- 开放防火墙端口80
>  `firewall-cmd --zone=public --add-port=80/tcp --permanent`
>
>- 关闭80端口
>
>- `firewall-cmd --zone=public --remove-port=80/tcp --permanent `
>
>- 配置立即生效
>
>- `firewall-cmd --reload `
>
>- 查看防火墙状态
>
>- `systemctl status firewalld`
>
>- 关闭防火墙
>
>- `systemctl stop firewalld`
>
>- 打开防火墙
>
>- `systemctl start firewalld`
>
>- 开放一段端口
>
>- `firewall-cmd --zone=public --add-port=8121-8124/tcp --permanent`
>
>- 查看开放的端口列表
>
>- `firewall-cmd --zone=public --list-ports`

### 6、RPM和YUM

#### 6.1、RPM包简单查询指令

> `rpm -qa | grep xx` （查询以安装的rpm列表）

>`rpm -qa` ：查询所安装的所有rpm软件包
>
>rpm -qa | more
>
>rpm -qa | grep X [rpm -qa | grep firefox ]
>
>
>
>`rpm -ql  软件包名`：查询软件包中的文件
>
>rpm  -ql  firefox
>
>
>
>`rpm  -qf  文件全路径名`  查询文件所属的软件包
>
>rpm -qf  /etc/passwd
>
>rpm -qf /root/install.log
>
>
>
>`rpm -q 软件包名`：查询软件包是否安装
>
>rpm -q firefox
>
>
>
>`rpm  -qi  软件包名`：查询软件包信息
>
>rpm -qi  file

#### 6.2、rpm包卸载

> `rpm -e RPM包的名称`

#### 6.3、rpm包安装

> `rpm -ivh RPM包的全路径名称`
>
> 1. i = install  安装
> 2. v = verbose  提示
> 3. h = hash  进度条

#### 6.4、yum包

>查询yum服务器是否有需要安装的软件
>
>`yum list | grep xx软件列表`
>
>安装指定的yum包
>
>`yum install xxx下载安装`	

解决问题网站

https://blog.csdn.net/manong__/article/details/122842090

https://www.ocxd.cn/post/95.html

yum install -y perl

CXy_agdrG5A

CXy_agdrG5A;

### 7、Linux中JAVAEE环境搭建

mysql密码：Ljzyou2513@

https://www.cnblogs.com/czy552/p/14851814.html

1. 使用命令`netstat -an|grep 3306`查看是否开启端口

2. 使用命令`firewall-cmd --zone=public --query-port=3306/tcp`查看防火墙是否开启`3306`.

3. 如果没有开启，则用`firewall-cmd --zone=public --add-port=3306/tcp`开启

4. 使用命令`service mysqld start`开启`mysql`服务

5. 进入mysql数据库，开启远程权限：mysql -u root -p;  use mysql

6. - `CREATE USER 'root'@'%' IDENTIFIED BY '密码';`
   - `ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '密码';`
   - `GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;`(赋予新用户所有权限)
   - `flush privileges;`
   
7. 检查是否设置正常：`select host,user,plugin from user`;

   解决错误网站

https://blog.csdn.net/liurui50/article/details/105478422

*)n-.dRRA4t5

安装教程网站

https://www.cnblogs.com/yanglang/p/10782941.html  

https://blog.csdn.net/qq_36902628/article/details/118997046

`https://blog.csdn.net/Jackie_vip/article/details/105762378`

### 8、安装redis

1. 解压redis压缩包，进入到redis目录  tar -zxvf redis-6.2.6.tar.gz

   ![image-20220202195320436](C:\Users\86157\AppData\Roaming\Typora\typora-user-images\image-20220202195320436.png)

2. 安装c++环境  yum install gcc-c++

3. 执行make命令后执行make install命令

4. 进入到 usr/local/bin目录，将redis.conf文件拷贝到单前目录下

   ![image-20220202200615157](C:\Users\86157\AppData\Roaming\Typora\typora-user-images\image-20220202200615157.png)

5. 修改拷贝后的redis.conf

   ![image-20220202200815863](C:\Users\86157\AppData\Roaming\Typora\typora-user-images\image-20220202200815863.png)

6. 在usr/local/bin目录下启动redis服务

[root@hecs-276533 bin]# 	redis-server ./redisConfig/redis.conf 
[root@hecs-276533 bin]# redis-cli
127.0.0.1:6379> auth ljzyou2513

```
nohup java -jar xxxx.jar &
```

docker run --name mysql -e MYSQL_ROOT_PASSWORD=ljzyou2513 -d -p 3307:3306 mysql:8.0.26

### 9、docker-compose部署容器

```yml
version: "3.0"
services:
  mysql:
    image: mysql:8.0.26
    ports:
      - "3306:3306"
    volumes:
      - "/root/mysql/data:/var/lib/mysql"
      - "/root/mysql/conf:/etc/mysql/conf.d"
      - "/root/mysql/logs:/var/log/mysql"
      - "/etc/localtime:/etc/localtime:ro"
    environment:
      - "MYSQL_ROOT_PASSWORD=ljzyou2513"
      - "TZ=Asia/Shanghai"
      - "SET_CONTAINER_TIMEZONE=true"
      - "CONTAINER_TIMEZONE=Asia/Shanghai"
    restart: always

  redis:
    image: redis:6.2.6
    privileged: true
    volumes:
      - "/root/redis/data:/data"
      - "/root/redis/conf:/usr/local/etc/redis/redis.conf"
      - "/root/redis/logs:/logs"
    command: ["redis-server","/usr/local/etc/redis/redis.conf"]
    ports:
      - "6379:6379"
    environment:
      - "TZ=Asia/Shanghai"
    restart: always

  nginx:
    image: nginx:1.18.0
    restart: always
    hostname: nginx
    privileged: true
    ports:
      - "80:80"
    volumes:
      - "/root/nginx/conf/nginx.conf:/etc/nginx/nginx.conf"
      - "/root/nginx/html:/usr/share/nginx/html/"
      - "/root/nginx/logs:/var/log/nginx/"

  rabbitmq:
    image: rabbitmq:3.8.30
    restart: always
    volumes:
      - "/root/rabbitmq/data:/var/lib/rabbitmq/"
    ports:
      - "5672:5672"
      - "15672:15672"

```

