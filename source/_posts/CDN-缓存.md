---
title: CDN 缓存
date: 2017-04-05 10:53:22
tags: [CDN, 缓存]
categories: [技术]
---

在介绍CDN之前，我们先在脑海里面记住下面几个问题基本的问题, 然后通过后面的内容，逐步使这些基本的问题得到解答。

```
-- CDN 是什么？
-- CDN 有什么作用？
-- CDN 怎么用？
-- CDN 的原理是什么？
```

---

# CDN 概述 #

下面的这张Xmind图梳理了自己理解的关于CDN的知识图点，先从原理上介绍，然后通过一个实例了解到CDN的具体使用（试用具体产品的是了解一个东西很好的切入点），最后面对一些相关的扩展问题进行介绍。写几十博客的时候，使用Xmind之类的思维导图软件整理知识点是一种非常好的方法，通过这种层次性的关系，能够帮你理清楚思路。

![](cdn_knowledge_map.svg)

---
## 什么是CDN ##

CDN 的全称是 Content Delivery Network， 内容分发网络，是一种通过缓存服务器进行网站加速的方式。

CDN 解决的，是在复杂的网络情况下，很多地方用户访问网站速度慢的问题，其原理是在不同的地方建立缓存服务器，让用户就近访问。图片来源[MaxCDN Coupon](http://www.cdncoupon.com/maxcdn-coupon/)

<div align="center">
<img src="cdn.png" width="80%" align="center">
</div>

比如说，国内的宽带运营商有电信，网通，移动，长城等等，还有教育网，这些网络自己相互访问是很慢的，假如我是一个电信的用户，你的网站托管在了网通的机房里面，要直接访问网站，肯定慢得要死，但是如果把这个网站的内容在一个电信的服务器上面缓存起来，那么访问的时候，就不需要跨界到网通的线路上了，速度也自然会快起来。

---
## CDN的作用 ##

CDN的主要作用当然是缓存内容，增加用户访问网站的速度。在缓存用户的同时，CDN同时还在用户跟源站服务器之间建立了一道屏障，于是CDN同时还具有了降低源站访问压力和安全防护上面的作用，总结起来就是


> * __加速：__ 通过缓存数据，增加用户访问网站的速度
> * __减低源站压力：__ 因为很多的内容都直接从CDN的缓存服务器上面得到，所以源站服务器的压力就降低了
> * __访问控制：__ CDN还可以提供防盗链一类的作用，对资源的访问进行控制
> * __安全防御：__ 启用了CDN 以后，源站的IP是被隐藏的，所以通过域名进行的攻击都会被挡在CDN服务器上面，作为专业的解决方案供应商，CDN 服务器在安全防御上面比一般的网站会做的更好

关于第二点减轻源站压力，如果CDN的回源算法不合理，比如说几个CDN节点同时从源站拉取缓存的数据，同样也会对源站服务器造成很大的压力。

---
# CDN的结构和原理 #

## CDN的基本结构 ##

一个CDN加速系统要运行起来，包含三个基本的部分

> * __源站：__ 这个是用户想想要访问的网站，是所有内容的来源
> * __CDN缓存服务器：__ CDN将源站的内容缓存到某些服务器，这些服务器可能在不同的地方将相同的数据缓存多份，然后通过一些调度算法，将用户的请求引导到最快的一个。
> * __DNS：__ 当用户输入一个网址进行访问的时候，最终被发送到哪个ip地址，是由DNS决定的，CDN通过设置CNAME的方式，将用户的请求从源站转移到CDN的服务器的。

下面这张图表明了CDN 的基本流程和原理，图片来自[青云](https://docs.qingcloud.com/guide/cdn.html)

<div align="center">
<img src="cdn_principle.png" width="80%" align="center">
</div>

深入到CDN的实现，其内部也是一套web service的系统，会有load balance，分层系统，存储，调度算法等等，可以参考[阿里云的实现](http://peiqiang.net/pictures/aliyun/阿里CDN技术揭秘.pdf)

---
## 缓存 ## 

CDN 最主要的作用是对网站的内容进行缓存，那么对应的就有两个问题

> * CDN缓存哪些内容？全部还是部分？
> * 这些缓存数据的有效期是怎么样的，如何更新呢？

### CDN 缓存哪些内容 （应用场景） ###

既然是作为缓存的内容，那应该是可以重复访问的内容，这样缓存才有意义，如果每次访问得到的结果都不一样，那么CDN服务器还是需要访问源站, 那缓存的功能就失效了。另外一类就是访问频率比较高的热点资源，比如说新推出的App游戏更新，社交网络的热门图片。

所以通常情况下，CDN 说缓存的，是 **静态** 的内容，比如说网站的html/css/js文件，图片，音频，视频等等。随便打开一个CDN供应商的网站，在业务类型里面，就可以看到CDN支持的应用场景了。比如说[Azure CDN](https://www.azure.cn/documentation/articles/cdn-overview/)就提供下面的业务类型。

> * __网站、小文件加速:__ html网页文件，图片文件，JavaScript，CSS文件等
> * __大文件下载分发:__ 典型的用户场景如操作系统固件升级，手机APP更新等
> * __流媒体加速:__ 各类媒体网站，手机APP客户端
> * __HTTPS安全加速:__ 针对HTTPS协议的缓存加速服务，需要域名对应的certificate

### CDN 缓存策略（失效与更新） ###

有缓存就有有效期和更新时间的问题，对CDN来说，通常采取下面的方法进行处理

> __Pull:__ 为缓存内容设置最大有效期，有效期到了以后自动去pull数据更新
> __Push:__ 后台控制，手动更新缓存

CDN 节点访问源站点过程，就叫做回源，前面在讲CDN的作用的时候提到过，如果CDN的回源算法不合理，也会对源站服务器造成很大的压力。

[阿里云CDN](https://help.aliyun.com/document_detail/27136.html?spm=5176.doc27112.6.576.6RwaW8)的这张图很好的描述了CDN 服务的缓存策略和配置

<div align="center">
<img src="cache_strategy.png" width="90%" align="center">
</div>

---
## DNS ##

### CNAME ###
用户访问一个网站的时候，最主要的两步就是

> 1. 输入网址，DNS 服务器解析网址并转换成IP, 在通过路由寻址找到IP所在的服务器
> 2. 通过特定的端口(http的80端口或者https的443端口)访问服务器上的内容

在正常的情况下，NDS根据域名解析出来的IP地址，都是源站服务器的ip，如何让DNS将用户引导到CDN服务器上呢？方法就是使用[CNAME](https://en.wikipedia.org/wiki/CNAME_record), 通常情况下，DNS解析会有两种类型，一个是AName, 对应的是服务器的 ip 地址， CNAME则是别名，可以对应到另外一个域名，通过CNAME就可以将源站的流量引导到CND服务器上面了。下面是在阿里云万网申请域名以后，添加解析的页面。

<div align="center">
<img src="domain_cname.png" width="90%" align="center">
</div>

### 是否启动CDN访问过程对比 ###
### CDN 的调度算法  ###
### 安全防御 ###

---
## HTTPS 支持 ##

随着网络的发展，安全问题也越来越受到重视，所以越来越多的网站都开始使用全站Https， 比如google，Amazon。
使用给网络带来了安全，但是对于传统的CDN业务却带来了新的新的问题。

Htpps的安全性来自于两个方面：

> 1. 内容是加密过的
> 2. 浏览器会验证服务器证书，要求证书是有效的（Trusted CA）, 并且是颁发给要访问的域名

于是对于启用了https的网站来说，当NDS将用户Route到CDN缓存服务器的时候，就会出现安全的问题了。
现在的解决方法是，将用户的域名证书上传到CDN供应商，这样CDN就可以用证书的私钥和用户建立https连接了。阿里CDN在[启用Https 协议下的CDN的时候，就需要用户提供源站的证书和私钥](https://help.aliyun.com/document_detail/27118.html?spm=5176.doc27141.6.570.yxBd0i)。

在建立了用户到CDN之间的连接以后，还有一个问题，就是回源的时候，CDN服务器跟源站之间的通信，理论上，用户到CDN跟CDN到源站是两个独立的过程，可以使用不同的协议和不同的证书，但是为了减少对源站的影响（是否启用CDN一样），应该是在两段通路上，都使用相同的https cert。

关于HTTPS 加速的详细分析，可以参考[CDN and SSL/TSL](https://www.incapsula.com/cdn-guide/cdn-and-ssl-tls.html?winzoom=1)

### Cert的别名：SAN ###

说到Https的cert，这里要提到一个问题，当我们通过浏览器查看一个https网站的cert的时候，可能看到的Issued信息并不是这家网站本身，比如说打开azure.cn，查看其cert信息，会发现这个cert并不是给azure.cn，而是给一个叫做ssl.chinanetcenter.com的域名的，理论上这样的cert是无法通过验证的，但是在x509 的标准里面，包含了一个subject alternative names 的field，里面包含了一个域名的list，所有在这个list里面的域名，都会被当成授权过的。打开这张cert的details，azure.cn也确实包含在SAN list里面。

<div align="center">
<img src="san.png" width="90%" align="center">
</div>


---
## 其他问题 ##

在了解了CDN的原理和作用以后，整理一下思路，想想下面几个问题

__1.  一直在讲CDN，那么给我一个网站，我怎么知道是否启用了CDN呢？__

> 启用CDN的目的，是在不同的地方对数据进行多份缓存，加速访问，所以启用了CDN 的网站，从不同的地方去ping的话，会得到不同的 ip， 通过这个条件，就可以判断一个网站是否启用了CDN了。但是一般情况下，个人访问的接入点是不会变的，没办法做到从不同的地方去ping，好在有一些第三方的工具可以帮助我们做到这一点，比如[站长工具Ping](http://tool.chinaz.com/) 和360的[360 齐云测](http://ce.cloud.360.cn/)。下图就给出了站长工具对于域名microsoft.com的测试结果，从不同的地方ping www.microsoft.com, 可以得到29个独立的ip，于是我们就可以知道这个网站是启用了CDN的。

<div align="center">
<img src="ping_microsoft_com.png" width="80%" align="center">
</div>


__ 2. CDN 服务的计费模式__

> 作为一种公共的服务，CDN的计费模式代表了他能够解决的，用户最关心的问题，所以定价方案也是围绕着**缓存**这个关键字来进行的。 缓存是否有效，就是看承担了多少流量了，所以CDN的计费模式是根据流量来的，具体说包含三个方面：

>  * 数据流量
>  * 访问次数
>  * 峰值带宽

>更加详细的信息，可以参考各大CDN提供商的网站。

__ 3. 启用了CDN 以后, 是不是所有的流量都会经过CDN 呢?  __

> 答案是YES。
> CDN 作为一种服务, 除了缓存数据的服务器外,还有自己的调度服务器和相应的算法, 来决定将用户导流到哪个CDN节点。所以在配置域名解析的时候， 只配CNAME而不配ip，源站是隐藏的，决策权交给CDN的调度算法。

__ 4. CDN 只缓存静态内容和热点资源，那么剩下的资源如何访问呢？ __
> 上一个问题里面提到了，启用了CDN以后，所有的流量都是要经过CDN节点的，那么对于没有缓存的内容，CDN节点需要访问源站去获取，也就是所谓的回源。

__　5. 有可能会同一家网站，在世界各地使用不同提供商的CDN服务么， 这种情况下DNS怎么解决冲突？ __
> 答案也是YES, 
> 但是为了启用不同的CDN服务，网站需要在不同的地方有不同的域名，比如说微软在美国用Azure.com 提供云服务, 在中国则是用Azure.cn。当用户进行访问的时候，通过选择local或者网站根据用户的ip自动跳转到对应的域名，然后多CDN的问题就变成单个CDN的问题了。同样因为域名不一样，不同的CDN服务商都有对应的域名，所以也就不会有冲突了。

__6. 上面说到同一个网站在不同的地方会有不同的域名，那在启用https的时候，需要不同的cert吗？__
> 答案是都可以
> 在Certificate格式的x509标准里面，有个叫做SAN (Subject Alternative Names) 的field，它定义的就是这个Cert可以授权使用的域名的列表。通过设置SAN，同一张Cert就能够被多个不同的域名所公用了。除了同一个网站的不同地区的域名意外，同一家公司的不同网站，也可以使用一样的Cert,比如说Google.com 跟Youtube.com。


# Reference #

[CDN缓存那些事](https://www.qcloud.com/document/product/228/3236)
[阿里CDN技术揭秘](http://peiqiang.net/pictures/aliyun/阿里CDN技术揭秘.pdf)
[OpenSSL SAN 证书](http://liaoph.com/openssl-san/)
[SSL 数字证书的标准、编码以及文件扩展名](https://kangzubin.com/certificate-format/)
[CDN and SSL/TSL](https://www.incapsula.com/cdn-guide/cdn-and-ssl-tls.html?winzoom=1)
[别让CDN的回源把你的服务器拖垮，采用正确的回源策略](http://storysky.blog.51cto.com/628458/283145/)