#####**在前台用ajax调用后台控制器的方法，一样的方法却始终返回302错误，郁闷的折腾了几个小时。最后发现登录控制器被框架拦截器拦截并被shiro过滤，导致无法在登录控制器里直接加入方法，加入的方法无法直接进入就会返回302错误。另外在一个新的控制器里面写个需要的方法就搞定了。 <br>其实一开始的302错误就给了足够的提示。**

> 302 Found 是HTTP协议中的一个状态码(Status Code)。可以简单的理解为该资源原本确实存在，但已经被临时改变了位置；或者换个说法，就是临时的存在于某个临时URL下。通常会发送Header来暂时重定向到新的新位置。         


#####**EL表达式获取网站根路径**
<code>${pageContext.request.contextPath}</code>


#####**jQuery validation插件验证的多种方式**
1. 用类名来验证---->>><code>class="required email"</code>;
2. 单独增加属性---->>><code>equalTo="#password"</code>||<code>minlength="6"</code>;
3. 将校验规则写入js代码中；
4. 校验规则和submit分离;<pre>$("#form").validate({});
$("#btn).click(function(){
    if($("#form").valid()){
     $("#form").submit();
     }
});
</pre>  


#####**影响网络传输的因素**
-  网络带宽（一秒钟能够传输的比特数，b/s）
-  传输距离
-  TCP 拥塞控制(窗口 BDP = RTT * 带宽）