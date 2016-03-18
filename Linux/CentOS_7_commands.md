####**项目部署在Linux服务器上时，经常会有莫名其妙的错误，但是tomcat的错误提示又非常的少，可以在项目的classes目录下添加以下文件，logging.properties,然后可以在logs/servler-xxx里面查看详细信息。  logging.propertie内容如下：**

<pre><code>handlers = org.apache.juli.FileHandler, java.util.logging.ConsoleHandler 

############################################################ 
# Handler specific properties. 
# Describes specific configuration info for Handlers. 
############################################################ 

org.apache.juli.FileHandler.level = FINE 
org.apache.juli.FileHandler.directory = ${catalina.base}/logs 
org.apache.juli.FileHandler.prefix = servlet-examples. 

java.util.logging.ConsoleHandler.level = FINE 
java.util.logging.ConsoleHandler.formatter = java.util.logging.SimpleFormatter </code></pre>

####**修改Linux系统下数据库表的大小写问题：**
<font face="微软雅黑">在mysql配置文件<code>/etc/my.cnf</code>文件中的<code>\#ignore case </code>下增加<code>lower_case_table_names=1</code>。</font>
####**查看系统CPU核数和信息：**
- <code>lscpu </code>
- <code>cat /proc/cpuinfo </code>
- <code>grep 'model name' /proc/cpuinfo |wc -l </code>

####**VPN相关：**
1. 查看当前连接VPN的人员信息：<code>last | grep still | grep ppp</code> ;
2. 添加修改VPN账号<code>vi /etc/ppp/chap-secrets</code>;   

####**修改Linux系统上的时间显示，修改timezone,可以按中国地区时间显示:**
1. <code>timedatectl</code>--><font face="微软雅黑">查看时间日期详细情况.</font>
2. <code>timedatectl list-timezones</code>-->--><font face="微软雅黑">查看时间日期详细情况.</font>
3. <code>timedatectl list-timezones | grep Shanghai</code>--><font face="微软雅黑">查询中国的时区.</font>
4. <code>timedatectl set-timezone=Asia/Shanghai</code>--><font face="微软雅黑">设置服务器上的时区为中国时区.</font>

####**find命令和exec命令结合使用.&nbsp;&nbsp;例子：**
1. <font face="微软雅黑">在<code>tomcat/logs</code>目录下删除更改日期在五天以前的日志文件：<code>find . -name '*.log' -mtime +5 -exec {} \;</code></font>
2. <font face="微软雅黑">安全模式下，在<code>tomcat/logs</code>目录下删除更改日期在五天以前的日志文件：<code>find . -name '*.log' -mtime +5 -ok {} \;</code></font>
3.  <font face="微软雅黑">复制：<code>find . -name "*.log" -exec cp {} .. \;</code>
<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>exec后面可以执行任何的命令</b><br>

####**查看硬盘使用情况:**&nbsp;&nbsp;&nbsp;&nbsp;<code>df -hl</code>

####**Linux系统中普通用户无法登录情况：**
1. 使用<code>vipw</code>或者<code>cat /etc/passwd</code>查看相关用户shell是否是<code>/sbin/nologin</code>；
2. 用命令<code>usermod -s /bin/bash username</code>即可；


####**打包|压缩**
> 打包是指将一大堆文件或目录变成一个总的文件；压缩则是将一个大的文件通过一些压缩算法变成一个小文件。为什么要区分这两个概念呢？这源于Linux中很多压缩程序只能针对一个文件进行压缩，这样当你想要压缩一大堆文件时，你得先将这一大堆文件先打成一个包（tar命令），然后再用压缩程序进行压缩（gzip bzip2命令）。

####**与用户及用户组相关的文件:**
1. 用户<code> /etc/passwd</code>;
2. 用户密码<code> /etc/shadow</code>;
3. 用户组<code>/etc/group</code>;
4. 用户组加密文件<code>/etc/gshadow</code>;

####**搭建svn服务器**
1. 启动svn时不要到仓库位置，而是根目录。例如 `svnserve -d -r /var/svn/svnrepos`-->`svnserve -d -r /var/svn`
2. 配置文件中注意空格，前端的空格会导致启动服务失败
3. 可以修改默认的仓库位置, 在`/etc/sysconfig/svnserver`

####**配置自动集成环境 Jenkins+SVN+Tomcat+Maven+Nexus+Nginx**

####**用 find 查找丑陋的文件名**
`find . -regex '.*[^-\_./0-9a-zA-Z].*'`

####**第一个Shell脚本  Hello_World**
<pre>#!/bin/bash
# This is our first script.
echo 'Hello World!'</pre>
