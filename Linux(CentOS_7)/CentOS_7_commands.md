<<<<<<< HEAD
###**������Linux��������ʱ����������Ī������Ĵ��󣬵���tomcat�Ĵ�����ʾ�ַǳ����٣���������Ŀ��classesĿ¼����������ļ���logging.properties,Ȼ�������logs/servler-xxx����鿴��ϸ��Ϣ��  logging.propertie�������£�**
=======
###**项目部署在Linux服务器上时，经常会有莫名其妙的错误，但是tomcat的错误提示有非常的少，可以在项目的classes目录下添加以下文件，logging.properties,然后可以在logs/servler-xxx里面查看详细信息。  内容如下：**
>>>>>>> b6c67f521a6c526d789c6f67e925242809769c5d
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
