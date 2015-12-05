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

####**查看当前连接VPN的人员信息：**
<code>last | grep still | grep ppp</code>    

####**修改Linux系统上的时间显示，修改timezone,可以按中国地区时间显示:**
1. <code>timedatectl</code>--><font face="微软雅黑">查看时间日期详细情况.</font>
2. <code>timedatectl list-timezones</code>-->--><font face="微软雅黑">查看时间日期详细情况.</font>
3. <code>timedatectl list-timezones | grep Shanghai</code>--><font face="微软雅黑">查询中国的时区.</font>
4. <code>timedatectl set-timezone=Asia/Shanghai</code>--><font face="微软雅黑">设置服务器上的时区为中国时区.</font>

####**find命令和exec命令结合使用.&nbsp;&nbsp;例子：**
1. <font face="微软雅黑">在<code>tomcat/logs</code>目录下删除更改日期在五天以前的日志文件：<code>find . -name '*.log' -mtime +5 -exec {} \;</code></font>
2. <font face="微软雅黑">安全模式下，在<code>tomcat/logs</code>目录下删除更改日期在五天以前的日志文件：<code>find . -name '*.log' -mtime +5 -ok {} \;</code></font>
3.  <font face="微软雅黑">复制：<code>find . -name "*.log" -exec cp {} .. \;</code>
<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>exec后面可以执行任何的命令</b>