---
date: 2011-02-15
layout:  post
title:  HTTPS的七个误解
tagline:
categories:
- web
tags:
- https
---
<strong>HTTPS的七个误解</strong>  原文网址：<a href="http://blog.httpwatch.com/2011/01/28/top-7-myths-about-https/">http://blog.httpwatch.com/2011/01/28/top-7-myths-about-https/</a>  译者：阮一峰  <img src="http://image.beekka.com/blog/201102/bg2011021311.jpg">  <strong>误解七：HTTPS无法缓存</strong>  许多人以为，出于安全考虑，浏览器不会在本地保存HTTPS缓存。实际上，只要在HTTP头中使用特定命令，HTTPS是可以缓存的。 <!--more-->    微软的IE项目经理<a href="http://blogs.msdn.com/b/ieinternals/archive/2010/04/21/internet-explorer-may-bypass-cache-for-cross-domain-https-content.aspx">Eric Lawrence</a>写道：  

   "说来也许令人震惊，只要HTTP头允许这样做，所有版本的IE都缓存HTTPS内容。比如，如果头命令是Cache-Control: max-age=600，那么这个网页就将被IE缓存10分钟。IE的缓存策略，与是否使用HTTPS协议无关。（其他浏览器在这方面的行为不一致，取决于你使用的版本，所以这里不加以讨论。）"

  Firefox默认只在内存中缓存HTTPS。但是，只要头命令中有Cache-Control: Public，缓存就会被写到硬盘上。下面的图片显示，Firefox的硬盘缓存中有HTTPS内容，头命令正是Cache-Control:Public。  <img src="http://image.beekka.com/blog/201102/bg2011021301.png">  <strong>误解六：SSL证书很贵</strong>  如果你在网上<a href="http://www.google.com/search?q=cheap+SSL+certificates&ie=utf-8&oe=utf-8">搜一下</a>，就会发现很多便宜的SSL证书，大概10美元一年，这和一个.com域名的年费差不多。而且事实上，还能找到<a href="http://www.startssl.com/">免费</a>的SSL证书。  在效力上，便宜的证书当然会比大机构颁发的证书差一点，但是几乎所有的主流浏览器都接受这些证书。  <strong>误解五：HTTPS站点必须有独享的IP地址</strong>  由于IPv4将要分配完毕，所以很多人关心这个问题。每个IP地址只能安装一张SSL证书，这是毫无疑问的。但是，如果你使用子域名通配符SSL证书（wildcard SSL certificate，价格大约是每年125美元），就能在一个IP地址上部署多个HTTPS子域名。比如，https://www.httpwatch.com和https://store.httpwatch.com，就共享同一个IP地址。  <img src="http://image.beekka.com/blog/201102/bg2011021302.png">  另外，UCC（统一通信证书，Unified Communications Certificate）支持一张证书同时匹配多个站点，可以是完全不同的域名。<a href="http://en.wikipedia.org/wiki/Server_Name_Indication">SNI</a>（服务器名称指示，Server Name Indication）允许一个IP地址上多个域名安装多张证书。服务器端，Apache和Nginx支持该技术，IIS不支持；客户端，IE 7+、Firefox 2.0+、chrome 6+、Safari 2.1+和Opera 8.0+支持。  <strong>误解四：转移服务器时要购买新证书</strong>  部署SSL证书，需要这样几步：  

   "说来也许令人震惊，只要HTTP头允许这样做，所有版本的IE都缓存HTTPS内容。比如，如果头命令是Cache-Control: max-age=600，那么这个网页就将被IE缓存10分钟。IE的缓存策略，与是否使用HTTPS协议无关。（其他浏览器在这方面的行为不一致，取决于你使用的版本，所以这里不加以讨论。）"

  这些步骤都经过精心设计，保证传输的安全，防止有人截取或非法获得证书。结果就是，你在第二步得到的证书不能用在另一台服务器上。如果你需要这样做，就必须以其他格式输出证书。  比如，IIS的做法是生成一个可以转移的.pfx文件，并加以密码保护。  <img src="http://image.beekka.com/blog/201102/bg2011021303.png">  将这个文件传入其他服务器，将可以继续使用原来的SSL证书了。  <strong>误解三：HTTPS太慢</strong>  使用HTTPS不会使你的网站变得更快（实际上有可能，请看下文），但是有一些<a href="http://blog.httpwatch.com/2009/01/15/https-performance-tuning/">技巧</a>可以大大减少额外开销。  首先，只要压缩文本内容，就会降低解码耗用的CPU资源。不过，对于当代CPU来说，这点开销不值一提。  其次，建立HTTPS连接，要求额外的TCP往返，因此会新增一些发送和接收的字节。但是，从下图可以看到，新增的字节是很少的。  <img src="http://image.beekka.com/blog/201102/bg2011021304.png">  第一次打开网页的时候，HTTPS协议会比HTTP协议慢一点，这是因为读取和验证SSL证书的时间。下面是一张HTTP网页打开时间的瀑布图。  <img src="http://image.beekka.com/blog/201102/bg2011021305.png">  同一张网页使用HTTPS协议之后，打开时间变长了。  <img src="http://image.beekka.com/blog/201102/bg2011021306.png">  建立连接的部分，大约慢了10%。但是，一旦有效的HTTPS连接建立起来，再刷新网页，两种协议几乎没有区别。先是HTTP协议的刷新表现：  <img src="http://image.beekka.com/blog/201102/bg2011021307.png">  然后是HTTPS协议：  <img src="http://image.beekka.com/blog/201102/bg2011021308.png">  某些用户可能发现，HTTPS比HTTP更快一点。这会发生在一些大公司的内部局域网，因为通常情况下，公司的网关会截取并分析所有的网络通信。但是，当它遇到HTTPS连接时，它就只能直接放行，因为HTTPS无法被解读。正是因为少了这个解读的过程，所以HTTPS变得比较快。  <strong>误解二：有了HTTPS，Cookie和查询字符串就安全了</strong>  虽然无法直接从HTTPS数据中读取Cookie和查询字符串，但是你仍然需要使它们的值变得难以预测。  比如，曾经有一家英国银行，直接使用顺序排列的数值表示session id:  <img src="http://image.beekka.com/blog/201102/bg2011021309.png">  黑客可以先注册一个账户，找到这个cookie，看到这个值的表示方法。然后，改动cookie，从而劫持其他人的session id。至于查询字符串，也可以通过<a href="http://blog.httpwatch.com/2009/02/20/how-secure-are-query-strings-over-https/">类似方式</a>泄漏。  <strong>误解一：只有注册登录页，才需要HTTPS</strong>  这种想法很普遍。人们觉得，HTTPS可以保护用户的密码，此外就不需要了。Firefox浏览器新插件<a href="http://codebutler.com/firesheep">Firesheep</a>，证明了这种想法是错的。我们可以看到，在Twitter和Facebook上，劫持其他人的session是非常容易的。  咖啡馆的免费WiFi，就是一个很理想的劫持环境，因为两个原因：  

   "说来也许令人震惊，只要HTTP头允许这样做，所有版本的IE都缓存HTTPS内容。比如，如果头命令是Cache-Control: max-age=600，那么这个网页就将被IE缓存10分钟。IE的缓存策略，与是否使用HTTPS协议无关。（其他浏览器在这方面的行为不一致，取决于你使用的版本，所以这里不加以讨论。）"

  以Twitter为例，它的登录页使用了HTTPS，但是登录以后，其他页面就变成了HTTP。这时，它的cookie里的session值就暴露了。  <img src="http://image.beekka.com/blog/201102/bg2011021310.png">  也就是说，这些cookie是在HTTPS环境下建立的，但是却在HTTP环境下传输。如果有人劫持到这些cookie，那他就能以你的身份在Twitter上发言了。  （完）  <ul>

<li>版权声明：自由转载-非商用-非衍生-保持署名 | <a href="http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh">Creative Commons BY-NC-ND 3.0</a>
</li>    <li>原文网址：<a href="http://www.ruanyifeng.com/blog/2011/02/seven_myths_about_https.html">http://www.ruanyifeng.com/blog/2011/02/seven_myths_about_https.html</a>
</li>    <li>最后修改时间：2011年2月13日 21:21</li> </ul>
