###**Pro Git（中文版）在线地址**  http://git.oschina.net/progit/index.html

<font face ="微软雅黑">1：用git连接GitHub会有HTTPS和SSH两种协议，使用HTTPS时每次pull 和 push 时都要输入用户名和密码，比较麻烦。使用SSH时需要生成公钥和公钥(<code>$ ssh-keygen -t rsa -C "your_email@youremail.com"</code>),公钥要放进GitHub里面。**<font color="red">Git和GitHub自带的Git Shell需要生成不同的公私密钥。同样的路径下 Git Shell 可能会报错，暂时不知道原因。**</font>生成SSH密钥时需要输入passphrase,默认留空就好。可以用命令<code>$ ssh -T  git@github.com</code>来检测是否成功。查看remote url :
<code>$ git remote -v</code>，修改链接协议：<code>$ git remote set-url origin git@github.com:jinshengjiekk/Documents.git</code>，或者<code>$ git remote set-url origin https://github.com/jinshengjiekk/Documents.git</code></font>

<font face="微软雅黑">2:</font>

 