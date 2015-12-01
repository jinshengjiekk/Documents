###**部署在Linux服务器上时，经常会有莫名其妙的错误，但是tomcat的错误提示又非常的少，可以在项目的classes目录下添加以下文件，logging.properties,然后可以在logs/servler-xxx里面查看详细信息。  logging.propertie内容如下：**
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
