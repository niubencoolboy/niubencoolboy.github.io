---
layout: post
title: "常见三种web攻击详解以及Django防御"
date: 2019-04-20 20:00:00
author: "Johan Niu"
header-img : "img/post-bg-os-metro.jpg"
tags:
    - 攻击工具
    - 安全
    - Python
       
---

# 常见三种web攻击详解以及Django防御

## 1 SQL注入

### 1.1 定义

把SQL命令插入到Web表单提交、输入域名、页面请求的查询字符串（注入本质上就是把输入的字符串变成可执行的程序语句），最终达到欺骗服务器执行恶意的SQL命令。

具体来说，它是利用现有应用程序，将（恶意的）SQL命令注入到后台数据库引擎执行的能力，它可以通过在Web表单中输入（恶意）SQL语句得到一个存在安全漏洞的网站上的数据库，而不是按照设计者意图去执行SQL语句。

在Web应用漏洞中，SQL Injection 漏洞的风险要高过其他所有的漏洞。

## 1.2 实例
见上一篇博客：

[使用SQLMAP进行SQL注入实战](http://johan.vip/2019/04/19/use_sqlmap/)

### 1.3 防御

* 对用户的输入进行校验，可以通过正则表达式，或限制长度；对单引号和双"-"进行转换等。
* 不要使用动态拼装SQL，可以使用参数化的SQL或者直接使用存储过程进行数据查询存取。
* 不要使用管理员权限的数据库连接，为每个应用使用单独的权限有限的数据库连接。
* 不要把机密信息直接存放，加密或者hash掉密码和敏感的信息。

## 2 XSS

### 2.1 定义

XSS 全称(Cross Site Scripting) 跨站脚本攻击， 是Web程序中最常见的漏洞。

指攻击者在网页中嵌入客户端脚本(例如JavaScript), 当用户浏览此网页时，脚本就会在用户的浏览器上执行，从而达到攻击者的目的。

属于一种注入攻击，注入本质上就是把输入的数据变成可执行的程序语句，比如这些代码包括HTML代码和客户端脚本。

本质：将输入的数据变成可执行的脚步，使其在用户的浏览器上执行。

### 2.2 危害

* 获取cookie，盗取各类用户帐号，如机器登录帐号、用户网银帐号、各类管理员帐号
* 钓鱼，导航到恶意网站,携带木马等。
* 控制企业数据，包括读取、篡改、添加、删除企业敏感数据的能力
* 盗窃企业重要的具有商业价值的资料，非法转账等

### 2.3 分类
#### 2.3.1 反射型xss攻击

反射型XSS，又称非持久型XSS。

之所以称为反射型XSS，则是因为这种攻击方式的注入代码是从目标服务器通过错误信息、搜索结果等等方式“反射”回来的。

而称为非持久型XSS，则是因为这种攻击方式具有一次性。

原理图：

 ![反射性XSS原理图](http://niubencoolboy.github.io/img/sec/反射性XSS.png) 

攻击者通过电子邮件等方式将包含注入脚本的恶意链接发送给受害者，当受害者点击该链接时，注入脚本被传输到目标服务器上，然后服务器将注入脚本“反射”到受害者的浏览器上，从而在该浏览器上执行了这段脚本。

比如攻击者将如下链接发送给受害者：

	http://blog.johaniu.com/img?input=<script>alert("Hello,XSS!");</script>

当受害者点击这个链接的时候，注入的脚本被当作搜索的关键词发送到目标服务器的search.asp页面中，则在搜索结果的返回页面中，这段脚本将被当作搜索的关键词而嵌入。这样，当用户得到搜索结果页面后，这段脚本也得到了执行。这就是反射型XSS攻击的原理，可以看到，攻击者巧妙地通过反射型XSS的攻击方式，达到了在受害者的浏览器上执行脚本的目的。

由于代码注入的是一个动态产生的页面而不是永久的页面，因此这种攻击方式只在点击链接的时候才产生作用，这也是它被称为非持久型XSS的原因


#### 2.3.2 存储型xss攻击

存储型XSS，又称持久型XSS，他和反射型XSS最大的不同就是，攻击脚本将被永久地存放在目标服务器的数据库和文件中。这种攻击多见于论坛，攻击者在发帖的过程中，将恶意脚本连同正常信息一起注入到帖子的内容之中。随着帖子被论坛服务器存储下来，恶意脚本也永久地被存放在论坛服务器的后端存储器中。当其它用户浏览这个被注入了恶意脚本的帖子的时候，恶意脚本则会在他们的浏览器中得到执行，从而受到了攻击。

### 2.4 防御

* 字符过滤：对用户的请求无论是url还是表单提交的内容都进行长度检查和对特殊字符进行过滤
* cookie方面：避免在cookie中放入重要敏感信息
* Html encode，假如某些情况下，我们不能对用户数据进行严格的过滤，那我们也需要对标签进行转换
* 表单提交：尽量使用post的方式提交表单而不是get方式

在Django中，防御方法为：

	总是转义可能来自某个用户的任何内容。
	
采用参数化的方式，Django的模板会对所有变量自动进行转义。

	# views.py
	
	from django.shortcuts import render_to_response
	
	def say_hello(request):
	    name = request.GET.get('name', 'world')
	    return render_to_response('hello.html', {'name': name})


	# hello.html
	
	<h1>Hello, {{ name }}!</h1>


## 3 CSRF
### 3.1 定义

CSRF（Cross-site request forgery），中文名称：跨站请求伪造。

### 3.2 原理

1. 用户C打开浏览器，访问受信任网站A，输入用户名和密码请求登录网站A；
1. 在用户信息通过验证后，网站A产生Cookie信息并返回给浏览器，此时用户登录网站A成功，可以正常发送请求到网站A；
1. 用户未退出网站A之前，在同一浏览器中，打开一个TAB页访问网站B；
1. 网站B接收到用户请求后，返回一些攻击性代码，并发出一个请求要求访问第三方站点A；
1. 浏览器在接收到这些攻击性代码后，根据网站B的请求，在用户不知情的情况下携带Cookie信息，向网站A发出请求。网站A并不知道该请求其实是由B发起的，所以会根据用户C的Cookie信息以C的权限处理该请求，导致来自网站B的恶意代码被执行。

CSRF 攻击是黑客借助受害者的 cookie 骗取服务器的信任，但是黑客并不能拿到 cookie，也看不到 cookie 的内容。另外，对于服务器返回的结果，由于浏览器同源策略的限制，黑客也无法进行解析。因此，黑客无法从返回的结果中得到任何东西，他所能做的就是给服务器发送请求，以执行请求中所描述的命令，在服务器端直接改变数据的值，而非窃取服务器中的数据 。黑客无法获取cookie和session值更无法进行解析。 

### 3.2.1 CSRF攻击实例

受害者 Bob 在银行有一笔存款，通过对银行的网站发送请求 http://bank.example/withdraw?account=bob&amount=1000000&for=bob2 可以使 Bob 把 1000000 的存款转到 bob2 的账号下。通常情况下，该请求发送到网站后，服务器会先验证该请求是否来自一个合法的 session，并且该 session 的用户 Bob 已经成功登陆。

黑客 Mallory 自己在该银行也有账户，他知道上文中的 URL 可以把钱进行转帐操作。Mallory 可以自己发送一个请求给银行：http://bank.example/withdraw?account=bob&amount=1000000&for=Mallory。但是这个请求来自 Mallory 而非 Bob，他不能通过安全认证，因此该请求不会起作用。

这时，Mallory 想到使用 CSRF 的攻击方式，他先自己做一个网站，在网站中放入如下代码： src=”http://bank.example/withdraw?account=bob&amount=1000000&for=Mallory ”，并且通过广告等诱使 Bob 来访问他的网站。当 Bob 访问该网站时，上述 url 就会从 Bob 的浏览器发向银行，而这个请求会附带 Bob 浏览器中的 cookie 一起发向银行服务器。大多数情况下，该请求会失败，因为他要求 Bob 的认证信息。但是，如果 Bob 当时恰巧刚访问他的银行后不久，他的浏览器与银行网站之间的 session 尚未过期，浏览器的 cookie 之中含有 Bob 的认证信息。这时，悲剧发生了，这个 url 请求就会得到响应，钱将从 Bob 的账号转移到 Mallory 的账号，而 Bob 当时毫不知情。等以后 Bob 发现账户钱少了，即使他去银行查询日志，他也只能发现确实有一个来自于他本人的合法请求转移了资金，没有任何被攻击的痕迹。而 Mallory 则可以拿到钱后逍遥法外。 
        
### 3.2.2 工具

以CSRFTester工具为例，CSRF漏洞检测工具的测试原理如下：使用CSRFTester进行测试时，首先需要抓取我们在浏览器中访问过的所有链接以及所有的表单等信息，然后通过在CSRFTester中修改相应的表单等信息，重新提交，这相当于一次伪造客户端请求。如果修改后的测试请求成功被网站服务器接受，则说明存在CSRF漏洞，当然此款工具也可以被用来进行CSRF攻击。

### 3.3 防御

1. 验证 HTTP Referer 字段；
1. 在请求地址中添加 token 并验证；
1. 在 HTTP 头中自定义属性并验证。

### 3.4 Django中的防御

使用CSRF中间件

django.contrib.csrf 开发包只有一个模块： middleware.py 。

该模块包含了一个 Django 中间件类—— CsrfMiddleware ，该类实现了 CSRF 防护功能。

CsrfMiddleware 的工作模式。 它完成以下两项工作：

1. 它修改当前处理的请求，向所有的 POST 表单增添一个隐藏的表单字段，使用名称是 csrfmiddlewaretoken ，值为当前会话 ID 加上一个密钥的散列值。 如果未设置会话 ID ，该中间件将 不会 修改响应结果，因此对于未使用会话的请求来说性能损失是可以忽略的。
1. 对于所有含会话 cookie 集合的传入 POST 请求，它将检查是否存在 csrfmiddlewaretoken 及其是否正确。 如果不是的话，用户将会收到一个 403 HTTP 错误。 403 错误页面的内容是检测到了跨域请求伪装。 终止请求。

局限性

CsrfMiddleware 的运行需要 Django 的会话框架。

如果你使用了自定义会话或者身份验证框架手动管理会话 cookies，该中间件将帮不上你的忙。

详情见：

[http://djangobook.py3k.cn 第十六章：集成的子框架 django.contrib](http://djangobook.py3k.cn/2.0/chapter16/)



参考链接：

[https://www.cnblogs.com/welan/p/9461153.html(SQL注入)](https://www.cnblogs.com/welan/p/9461153.html)

[https://blog.csdn.net/stpeace/article/details/53512283[CSRF攻击与防御（写得非常好)]](https://blog.csdn.net/stpeace/article/details/53512283)

[https://blog.csdn.net/juan0728juan/article/details/81087236(WEB三大攻击之—CSRF攻击与防护)](https://blog.csdn.net/juan0728juan/article/details/81087236)

[http://djangobook.py3k.cn/2.0/chapter20/(Django 安全)](http://djangobook.py3k.cn/2.0/chapter20/)