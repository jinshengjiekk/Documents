<<<<<<< HEAD
###**²¿ÊðÔÚLinux·þÎñÆ÷ÉÏÊ±£¬¾­³£»áÓÐÄªÃûÆäÃîµÄ´íÎó£¬µ«ÊÇtomcatµÄ´íÎóÌáÊ¾ÓÖ·Ç³£µÄÉÙ£¬¿ÉÒÔÔÚÏîÄ¿µÄclassesÄ¿Â¼ÏÂÌí¼ÓÒÔÏÂÎÄ¼þ£¬logging.properties,È»ºó¿ÉÒÔÔÚlogs/servler-xxxÀïÃæ²é¿´ÏêÏ¸ÐÅÏ¢¡£  logging.propertieÄÚÈÝÈçÏÂ£º**
=======
###**é¡¹ç›®éƒ¨ç½²åœ¨LinuxæœåŠ¡å™¨ä¸Šæ—¶ï¼Œç»å¸¸ä¼šæœ‰èŽ«åå…¶å¦™çš„é”™è¯¯ï¼Œä½†æ˜¯tomcatçš„é”™è¯¯æç¤ºæœ‰éžå¸¸çš„å°‘ï¼Œå¯ä»¥åœ¨é¡¹ç›®çš„classesç›®å½•ä¸‹æ·»åŠ ä»¥ä¸‹æ–‡ä»¶ï¼Œlogging.properties,ç„¶åŽå¯ä»¥åœ¨logs/servler-xxxé‡Œé¢æŸ¥çœ‹è¯¦ç»†ä¿¡æ¯ã€‚  å†…å®¹å¦‚ä¸‹ï¼š**
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
