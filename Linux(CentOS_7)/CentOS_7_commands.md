####**��Ŀ������Linux��������ʱ����������Ī������Ĵ��󣬵���tomcat�Ĵ�����ʾ�ַǳ����٣���������Ŀ��classesĿ¼����������ļ���logging.properties,Ȼ�������logs/servler-xxx����鿴��ϸ��Ϣ��  logging.propertie�������£�**

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

####**�޸�Linuxϵͳ�����ݿ��Ĵ�Сд���⣺**
<font face="΢���ź�">��mysql�����ļ�<code>/etc/my.cnf</code>�ļ��е�<code>\#ignore case </code>������<code>lower_case_table_names=1</code>��</font>
####**�鿴ϵͳCPU��������Ϣ��**
- <code>lscpu </code>
- <code>cat /proc/cpuinfo </code>
- <code>grep 'model name' /proc/cpuinfo |wc -l </code>

####**�鿴��ǰ����VPN����Ա��Ϣ��**
<code>last | grep still | grep ppp</code>    

####**�޸�Linuxϵͳ�ϵ�ʱ����ʾ���޸�timezone,���԰��й�����ʱ����ʾ:**
1. <code>timedatectl</code>--><font face="΢���ź�">�鿴ʱ��������ϸ���.</font>
2. <code>timedatectl list-timezones</code>-->--><font face="΢���ź�">�鿴ʱ��������ϸ���.</font>
3. <code>timedatectl list-timezones | grep Shanghai</code>--><font face="΢���ź�">��ѯ�й���ʱ��.</font>
4. <code>timedatectl set-timezone=Asia/Shanghai</code>--><font face="΢���ź�">���÷������ϵ�ʱ��Ϊ�й�ʱ��.</font>

####**find�����exec������ʹ��.&nbsp;&nbsp;���ӣ�**
1. <font face="΢���ź�">��<code>tomcat/logs</code>Ŀ¼��ɾ������������������ǰ����־�ļ���<code>find . -name '*.log' -mtime +5 -exec {} \;</code></font>
2. <font face="΢���ź�">��ȫģʽ�£���<code>tomcat/logs</code>Ŀ¼��ɾ������������������ǰ����־�ļ���<code>find . -name '*.log' -mtime +5 -ok {} \;</code></font>
3.  <font face="΢���ź�">���ƣ�<code>find . -name "*.log" -exec cp {} .. \;</code>
<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>exec�������ִ���κε�����</b>