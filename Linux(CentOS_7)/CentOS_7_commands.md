###**������Linux��������ʱ����������Ī������Ĵ��󣬵���tomcat�Ĵ�����ʾ�ַǳ����٣���������Ŀ��classesĿ¼����������ļ���logging.properties,Ȼ�������logs/servler-xxx����鿴��ϸ��Ϣ��  logging.propertie�������£�**
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
