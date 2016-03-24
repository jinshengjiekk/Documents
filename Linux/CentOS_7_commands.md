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

####**VPN��أ�**
1. �鿴��ǰ����VPN����Ա��Ϣ��<code>last | grep still | grep ppp</code> ;
2. ����޸�VPN�˺�<code>vi /etc/ppp/chap-secrets</code>;   

####**�޸�Linuxϵͳ�ϵ�ʱ����ʾ���޸�timezone,���԰��й�����ʱ����ʾ:**
1. <code>timedatectl</code>--><font face="΢���ź�">�鿴ʱ��������ϸ���.</font>
2. <code>timedatectl list-timezones</code>-->--><font face="΢���ź�">�鿴ʱ��������ϸ���.</font>
3. <code>timedatectl list-timezones | grep Shanghai</code>--><font face="΢���ź�">��ѯ�й���ʱ��.</font>
4. <code>timedatectl set-timezone=Asia/Shanghai</code>--><font face="΢���ź�">���÷������ϵ�ʱ��Ϊ�й�ʱ��.</font>

####**find�����exec������ʹ��.&nbsp;&nbsp;���ӣ�**
1. <font face="΢���ź�">��<code>tomcat/logs</code>Ŀ¼��ɾ������������������ǰ����־�ļ���<code>find . -name '*.log' -mtime +5 -exec {} \;</code></font>
2. <font face="΢���ź�">��ȫģʽ�£���<code>tomcat/logs</code>Ŀ¼��ɾ������������������ǰ����־�ļ���<code>find . -name '*.log' -mtime +5 -ok {} \;</code></font>
3.  <font face="΢���ź�">���ƣ�<code>find . -name "*.log" -exec cp {} .. \;</code>
<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b>exec�������ִ���κε�����</b><br>

####**�鿴Ӳ��ʹ�����:**&nbsp;&nbsp;&nbsp;&nbsp;<code>df -hl</code>

####**Linuxϵͳ����ͨ�û��޷���¼�����**
1. ʹ��<code>vipw</code>����<code>cat /etc/passwd</code>�鿴����û�shell�Ƿ���<code>/sbin/nologin</code>��
2. ������<code>usermod -s /bin/bash username</code>���ɣ�


####**���|ѹ��**
> �����ָ��һ����ļ���Ŀ¼���һ���ܵ��ļ���ѹ�����ǽ�һ������ļ�ͨ��һЩѹ���㷨���һ��С�ļ���ΪʲôҪ���������������أ���Դ��Linux�кܶ�ѹ������ֻ�����һ���ļ�����ѹ��������������Ҫѹ��һ����ļ�ʱ������Ƚ���һ����ļ��ȴ��һ������tar�����Ȼ������ѹ���������ѹ����gzip bzip2�����

####**���û����û�����ص��ļ�:**
1. �û�<code> /etc/passwd</code>;
2. �û�����<code> /etc/shadow</code>;
3. �û���<code>/etc/group</code>;
4. �û�������ļ�<code>/etc/gshadow</code>;

####**�svn������**
1. ����svnʱ��Ҫ���ֿ�λ�ã����Ǹ�Ŀ¼������ `svnserve -d -r /var/svn/svnrepos`-->`svnserve -d -r /var/svn`
2. �����ļ���ע��ո�ǰ�˵Ŀո�ᵼ����������ʧ��
3. �����޸�Ĭ�ϵĲֿ�λ��, ��`/etc/sysconfig/svnserver`

####**�����Զ����ɻ��� Jenkins+SVN+Tomcat+Maven+Nexus+Nginx**

####**�� find ���ҳ�ª���ļ���**
`find . -regex '.*[^-\_./0-9a-zA-Z].*'`

####**��һ��Shell�ű�  Hello_World**
<pre>#!/bin/bash
# This is our first script.
echo 'Hello World!'</pre>

####**Linux��������ȫ��¼**
�Լ��ڹ���ķ���������DDos�������������ң�������IP����CNNIC,�������ˡ��������沢û��ʲô��Ҫ�Ķ���������µ��Ǹ�������һ�Σ��������İ�ȫ����ͦ��Ҫ�ġ�
����װ��һ��CentOS7ϵͳ�������������������Ҫ���ľ��ǰ�ȫ��  
1. �޸�`/etc/ssh/sshd_config`�ļ�����ֹroot�û�Զ�̵�¼��   
2. �޸�`/etc/ssh/sshd_config`�ļ����ı�Ĭ�ϵ�22�˿ڣ�ͬʱ�޸ķ���ǽ����`/etc/sysconfig/iptables`��  
3. �½��û����Դ�Զ�̵�¼��ͬʱ��`/etc/ssh/sshd_config`�н�ֹ�����¼��������Կ��½��
  
������ ������Կ��½�Ĺ����������޷���¼�����⣬�����˺þã��������`~/.ssh`�� `~/.ssh/authorized_keys`��Ȩ�����⣬`~/.ssh`��Ȩ�ޱ�����`700` ,  `~/.ssh/authorized_keys` ��Ȩ�ޱ�����`600`�ſ��ԡ�   ͨ���� `/etc/ssh/sshd_config`��LogLevel���������debug3,���`/var/log/secure`���Բ��ҵ��������ڡ�

####**CentOS7 ��װshadowsocks**
�ϴη����������������������˷�����������װ�˼��ε�VPN,����޷�ʵ�ֿ�ѧ�������²������GFW����صķ����VPS�������ˡ� ���ѡ��shadowsocks��Ϊ��ѧ�����ķ������򵥵Ľ���ز����¼�£�
``` 
yum install epel-release
yum update
yum install python-setuptools m2crypto supervisor
easy_install pip
pip install shadowsocks 
vi /etc/shadowsocks.json
vi /etc/supervisord.conf
vi /etc/rc.local
(screen ssserver -c /etc/shadowsocks.conf)
 ```
 **shadowsocks �������finalspeed�����ټ�������Ч������**

