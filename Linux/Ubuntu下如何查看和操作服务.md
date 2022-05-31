# Ubuntu下如何查看和操作服务

### 一、程序、进程和服务的概念区分

**1、 程序**

这里的程序主要指应用程序，编写好的一段代码，这里介绍程序，主要是为了后面和进程做区分。

**2、进程**

操作系统最核心的概念就是进程。其实进程简单来说就是在操作系统中运行的程序，它是操作系统资源管理的最小单位。但是进程是一个动态的实体，它是程序的一次执行过程。我们如何产生一个进程呢？很简单的，就是“执行一个程序或指令”就会触发一个事件而取得一个PID。我们知道系统只认识二进制文件，那么当我们要让系统执行某个工作时，就需要启动（加载）某个二进制文件了。

**3、服务**

常驻内存的程序就会被我们称之为服务（daemon），服务一般都是一直在运行的命令，其独立于控制终端，不受前台和终端的控制，在后台周期性地执行某种任务或等待处理某些发生的事件。运行服务的进程通常被成为后台进程（守护进程）。服务由service启动，并配置。使用上通常由systemctl命令进行控制。常驻内存的程序：一般是系统或网络服务，例如工作调度的文件/etc/crontab，我们知道系统每分钟都会去扫描/etc/crontab以及相关的配置文件，来进行工作调度；常见的系统服务：crond,atd,rsyslogd，这些进程都是结果带d，daemon的缩写；Linux希望我们可以简单的判断该程序是否为daemon，所以一般daemon的程序都会加上d在文件名后；常见的网络服务：通常会启动一个负责网络监听的端口；

**4、进程和程序的区别**

进程是动态的，程序是静态的，进程是运行中的程序；

而程序是一些保存在硬盘上的可执行代码。

**5、进程与服务的区别**

进程在执行完任务就会死亡（关闭窗口或执行完命令），因此只会在内存中停留一会儿；

服务是一直在内存中运行着的进程；

### 二、服务分类

服务(service) 本质就是进程，但是是运行在后台的，因此我们又称为守护进程，是 Linux 中非常重要的知识点。系统的服务分类非常多，大致分为系统本身所需的系统服务、网络服务等。

**1.系统服务**

常见的系统服务：crond、atd、rsyslogd等等

**2.网络服务**

网络服务通常会启动一个负责网络监听的端口，等待其它程序的请求。常见的网络服务：tomcat、named、postfix、vsftpd等，结束服务也可以直接杀死其后台的进程。

### 三、服务操作命令

**1、service指令：**

service 服务名 [start | stop | restart | reload | status]

**2、systemctl指令：**

systemctl [start | stop | restart | reload | status] 服务名

示例：

下图两个命令都是查看mysql服务的状态，效果相同。

![image-20211128175919186](/home/summerice/.config/Typora/typora-user-images/image-20211128175919186.png)

关于服务状态可以看到上图里面有loaded、active等，下面解释一下：

**loaded**       系统服务已经初始化完成，加载过配置

**active（running）**    正有一个或多个程序正在系统中执行， vsftpd就是这种模式

**atcive（exited）**    僅執行一次就正常結束的服務， 目前並沒有任何程序在系統中執行

**atcive（waiting）**    正在執行當中，不過還再等待其他的事件才能继续处理

**inactive**      服务关闭

**enbaled**      服务开机启动

**disabled**      服务开机不自启

**static**      服务开机启动项不可被管理

**failed**      系统配置错误

**3、chkconfig指令**

查看/设置服务运行状态级别

a、查询所有服务：chkconfig --list

b、查看特定服务运行级别/运行状态（以sshd为例）：

chkconfig --list | grep 服务名

c、设置某个服务在某个运行状态下是否自启动

chkconfig --level 运行级别（0-6） on/off

*需要注意的是Ubuntu 20.04默认安装不支持chkconfig，需要安装一下*

### 四、Ubuntu没有chkconfig服务解决办法

ubuntu下chkconfig命令可以使用sysv-rc-conf 服务管理程序代替,只需要apt安装即可.

......建议百度此处内容

**五、chkconfig与Ubuntu运行级别**

关于运行级别简单地说，比如你安装了mysql，并且你把启动的脚本放在了/etc/rc.d/init.d目录下，有时候你需要开机**自动启动**它，而有时候则不需要，因此，就可以使用chkconfig命令来进行控制，这个命令就相当于一个开关，不过这个开关有6个档，表示在不同级别下的运行状态是on还是off。 

Ubuntu（基于debian）有8个运行级别，比其他非debian系统多一个S级。

Ubuntu的默认开机的runlevel是5，也就是用使用GUI显示管理器正常启动系统，可以用’runlevel’命令来查看当前的默认运行级别。

也就是看什么级别的时候需要自启动该服务，那么就需要修改配置，将对应的级别设置为on，可以通过命令设置，也可以通过修改文件设置。

对于chkconfig和运行级别更详细解释请参考：https://www.cnblogs.com/yongxingg/p/3651859.html

**附录：**

**1、systemctl命令**

systemctl list-units      ##列出当前系统服务的状态

systemctl list-unit-files    ##列出服务的开机状态

systemctl status postgresql      ##查看指定服务的状态

systemctl stop postgresql       ##关闭指定服务

systemctl start postgresql      ##开启指定服务

systemctl restart postgresql     ##从新启动服务

systemctl enable postgresql      ##设定指定服务开机开启

systemctl disable postgresql     ##设定指定服务开机关闭

systemctl reload postgresql      ##使指定服务从新加载配置

systemctl list-dependencies postgresql  ##查看指定服务的倚赖关系

systemctl mask postgresql      ##冻结指定服务

systemctl unmask postgresql      ##启用服务

systemctl set-default multi-user.target ##开机不开启图形

systemctl set-default graphical.target ##开机启动图形

**3、service命令**

查看当前所有服务：service --status-all

开启当前服务：service 服务名 start

关闭当前服务：service 服务名 stop

重启当前服务：service 服务名 restart

查看当前服务状态：service 服务名 status

**2、chkconfig用法**

chkconfig命令可以用来检查、设置系统的各种服务

使用语法：

chkconfig [--add][--del][--list][系统服务] 或 chkconfig [--level <等级代号>][系统服务][on/off/reset]

参数用法：

--add增加所指定的系统服务，让chkconfig指令得以管理它，并同时在系统启动的叙述文件内增加相关数据。

--del删除所指定的系统服务，不再由chkconfig指令管理，并同时在系统启动的叙述文件内删除相关数据。

--level<等级代号>à指定读系统服务要在哪一个执行等级中开启或关毕。

使用范例：

chkconfig --list   #列出所有的系统服务

chkconfig --add httpd #增加httpd服务

chkconfig --del httpd #删除httpd服务

chkconfig --level httpd 2345 on #把httpd在运行级别为2、3、4、5的情况下都是on（开启）的状态。

chkconfig命令提供了一种简单的方式来设置一个服务的运行级别。例如，为了设置MySQL服务器在运行级别3和4上运行，你必须首先将MySQL添加为受chkconfig管理的服务：

chkconfig --add mysql

现在，我们在级别3和5上设定服务为“on”

chkconfig --level 35 mysql on

在其他级别上设为off

chkconfig --level 01246 mysql off

为了确认你的配置被正确的修改了，我们可以列出服务将会运行的运行级别，如下所示：

\#chkconfig --list mysql

mysql      0:off    1:off    2:off    3:on 4:off    5:on 6:off