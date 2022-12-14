## linux

**虚拟机连接网络的三种方式**

​	1，**桥接模式**：虚拟系统直接虚拟出一个IP地址，会占用该网络内的IP地址

​	2，**NAT模式**：通过虚拟出一个地址，再通过代理的方式联网，要注意的是，nat模式时物理主机是找不到虚拟主机的IP地址的。

​	3，**主机模式**



**虚拟机克隆**

需求：做集群需要多台虚拟机，反复安装很麻烦，可以通过克隆的方式批量生成虚拟机。



**虚拟机快照**：相当于git仓库的版本系统，可以让将虚拟机某一状态存储下来。当系统出现异常后可以快速回到过去的一个状态。



**虚拟机迁移**：将虚拟机所在文件夹剪切走即可。



**vmTool**：物理机向虚拟机传文件需要用到。可以打通linux和虚拟机之间的硬盘，创造出一个物理机和虚拟机都可以访问的一个路径.



**Linux目录结构**:每个硬件都会在linux中映射成为文件来管理.

​	bin:存放着常用指令.

​	sbin:存放着管理员的东西

​	home:存放普通用户的主目录

​	root:系统管理员的主目录

​	lib:动态链接库

​	etc:系统管理所需的配置文件与子目录.

​	usr:很多应用程序安装的位置

​	boot:启动linux的一些核心文件

​	proc:一个虚拟目录,系统内存的映射?

​	srv:存放启动后需要提取的信息

​	sys:别动~~

​	tmp:存放临时文件

​	dev:设备管理器

​	media:会识别一些外部的文件,U盘,光驱等,识别成功后会挂载到该目录下.

​	mnt:让用户临时挂载别的文件系统,例如VMwareTool的共享目录就是将windows的目录挂载到了Linux中

​	opt:可以理解为安装包存放的位置,可以将需要安装的文件临时存放到该位置上.

​	/usr/local:给主机额外安装软件存放的目录,一般是通过源码编译安装的文件会存放到这.

​	/var:存放着经常变更的东西,例如日志等.

​	/selinux:关于linux的安全程序.



**xshell与xftp**：两者是用于远程登录与传送文件的工具。可以下载个人免费版



**vim编辑器**：linux中内置的一个文本编辑器，通过vim指令编辑，进入编辑区后有三种模式，普通模式仅供查看，

“:命令"，命令行模式，编辑模式。输入i，I，a，A等字符进入编辑模式则可以对文本进行编辑，下面介绍再普通模式下的一些常用指令。

​	1，复制，“行数+yy”，“p”粘贴

​	2，删除，“行数+dd”

​	3，查找，“/”,回车确认查找内容，n，切换查找字段所在位置。

​	4，显示行号，“set nu"，取消显示行号，”set nonu“

![image-20221025184745835](C:\Users\LiuXinXin\AppData\Roaming\Typora\typora-user-images\image-20221025184745835.png)

​	5，定位到文件的首行与末尾，普通模式下输入”gg"即可，“G”最后一行

​	6，撤销输入的内容，普通模式下输入“u”

​	7，显示行号，”set	nu",	"set	nonu"

​	8，定位到指定行，“数字+shift+G”



**关机与重启指令**

​	1，shutdown -h 1：代表一分钟后关机

​	2，shutdown -h now：立刻关机

​	3，shutdown -r now：立刻重启

​	4，halt：关机

​	5，reboot：重启

​	4，sync：将内存中的数据同步到磁盘。



**多用户**

​	1，创建用户	useradd	-d	目录名称	用户名

​	2，修改密码	passwd	用户名	如果不加上，默认是当前登录的用户。

**用户切换**

​	1，登录root用户	-su - root

​	2，注销root用户	-logout	!图形化界面该条指令是无效的

​	3，删除普通用户	-userdel	用户名	这种删除方式是不删除用户资料的，也就是说保留/home下的资料。如何删除其所有 资料呢？在 -userdel后面加上 “-r” 即可，一般是不选择删除的，忘掉它。

**用户信息**

​	1，查看用户信息 id	用户名

​	2，切换用户，就是前面讲的关于切换root用户的指令，而且从高权限切换到低权限用户是不需要密码的。

​	3，查看当前登录用户信息，who am i

**用户组**

​	描述：linux是多用户系统，用户与用户的权限往往是不相同的，为了方便管理，可以将用户分组，同组的用户会具备相似的权限。

​	1，创建组	groupadd	组名

​	2，删除组	groupdel	组名

​	3，创建用户时设置组	useradd	-r	组名	用户名	如果不指定用户的组名，则系统会自动创建出一个与用户名相同的组。

​	4，修改用户组	usermod	-g（G）	用户组名	用户名，如果没有用G，则用户名不会出现在group文件中的。



**用户组文件**

​	1，/etc/passwd：用户组的配置文件，记录着用户名，用户口令，用户id，组id，组描述信息，用户家目录，shell

![image-20221026103245906](C:\Users\LiuXinXin\AppData\Roaming\Typora\typora-user-images\image-20221026103245906.png)

​	2，/etc/shadow：口令配置信息，组成部分是用户名，加密口令，最后一次修改时间，最小时间间隔，最大时间间隔警告时间，不活动时间，失效时间，标志

![image-20221026103609278](C:\Users\LiuXinXin\AppData\Roaming\Typora\typora-user-images\image-20221026103609278.png)

​	3，/etc/group：组配置文件，组成部分包含组名，口令，标识号，组内用户

![image-20221026104251915](C:\Users\LiuXinXin\AppData\Roaming\Typora\typora-user-images\image-20221026104251915.png)



**显示时间**

​	1，直接显示完整时间，date

​	2，截取部分时间，date	“+%Y(y)+%m+%d+%h+%m+s,,,,,用的时候再查吧”



**查找命令**

​	1，通过文件名查找：find	“文件名”

**locate指令**

​	locate指令是指创建一个存储文件系统的数据库，通过该数据库去查询文件并不会从文件目录中找，因此可以加快速度，使用前要使用updatedb创建或更新。

**which指令**

​	查看指令在哪个目录下。

**grep指令**

​	查找内容，常常与管道符结合“|”，例如“cat”



**CROND**：定时执行某些任务

​	案例

![image-20221030112551419](C:\Users\LiuXinXin\AppData\Roaming\Typora\typora-user-images\image-20221030112551419.png)

​	日期设置分为三部分，区间为day【0，24】，一年中的一天，与一年中的一周，第一项与后两项的关系为and，后两项关系为or。

​	特殊的时间表示形式：

​	1，【,】表示时间戳

​	2，【-】表示连续的区间

​	可选参数

​	1，【-e】：编辑任务

​	2，【-l】：查看任务

​	3，【-r】：移除任务

**AT定时任务**

​	概念：前面的任务是无限次的任务，只要不被取消，当条件满足便可以无限次触发，但有时候我们只需要任务执行一遍，这时候就需要用到AT定时任务。

​	作业队列： 所有的定时任务都会存储在这里，atd守护进程默认会每分钟扫描一次作业队列，符合条件的会直接触发执行，执行过后任务会被直接移除，不会再次执行。

​	![image-20221031102956157](C:\Users\LiuXinXin\AppData\Roaming\Typora\typora-user-images\image-20221031102956157.png) 	参数	![image-20221031103535371](C:\Users\LiuXinXin\AppData\Roaming\Typora\typora-user-images\image-20221031103535371.png)

​	写入任务时backspace会被锁定，按住ctrl键解锁



​	**磁盘管理**：Linux中对磁盘的管理采用采用挂载的方式，（物理单元到逻辑单元的映射）采用mvr磁盘类型的Linux系统最多可以划分四个主磁盘区域。

​	划分步骤

​	1，分区：fdisk	/dev/磁盘名

​	2，格式化磁盘：mkfs	-t	文件系统	/dev/磁盘分区号

​	3，挂载mount	/dev/磁盘分区号	目录

​	！通过命令行挂载的方式，每一次重启后都会失效，需要手动开启。可以编辑文件：/etc/fstab

​	查看磁盘使用情况的命令：df	-h

​	查看磁盘占用情况的命令：du	-h,s,a,c	--max-depth = num(子节点深度)

​	查看所有系统磁盘详细信息

​	

​	**网络配置**

​	1，设置静态ip

​	/etc/sysconfig/network-script/网络连接名，可通过ifconfig查看，若为最小化安装，则需要先安装net-tools才能使用该命令

​	重启网络命令：service	network	restart

![image-20221101111559118](C:\Users\LiuXinXin\AppData\Roaming\Typora\typora-user-images\image-20221101111559118.png)

​	**进程管理**

​	1，查看所有进程	ps -aux	

![image-20221101113749749](C:\Users\LiuXinXin\AppData\Roaming\Typora\typora-user-images\image-20221101113749749.png)

​	pid表示进程名

​	command表示进程名，（执行该进程的指令）

​	stat表示进程状态,s表示休眠，z表示僵死进程

​	start表示进程启动的时间点

​	time表示进程运行的时长

​	2，显示进程的父子关系	ps	-ef

​	3，结束进程：kill	进程号；killall	进程名	

​	4，进程树：pstree	-up【显示进程所属用户与进程号】

​	**解压缩**

​	1，gzip	gunzip

​	2，zip	unzip

​	3，tar

​	**文件权限**

​	1，查看：ls	-ahl

​	2，权限组成部分，第一位表示文件类型，之后的字码三位一组，分别表示用户权限，同组其它用户权限，其他用户权限。

![image-20221102111614990](C:\Users\LiuXinXin\AppData\Roaming\Typora\typora-user-images\image-20221102111614990.png)

​	3，文件所有人修改：文件可以是属于用户，也可以是某个组的，chown，chgrp，可以选择参数-r表示递归

​	4，文件权限修改：

​		第一种方式	chmod	权限码	文件，r的数值为4，w为2，x为1,

​		第二种方式	chmod	oug=wrx	文件	——这个例子表示将此文件的权限修改为所有人都可读可写可执行。

​	**防火墙**

​	1，端口放行：firewall-cmd --zone=public --add-port=80/tcp --permanent
