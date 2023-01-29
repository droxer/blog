---
title: Https Explanation
date: 2016-01-05
scheduled: 2016-01-05
tags:
  - security
  - web
layout: layouts/post.njk
---
最近在看关于网络安全的一些知识，顺便又回顾了一下https的工作原理，这里根据自己的理解总结梳理。

首先，我们要知道https并不是一个新的协议，它是通过在http协议基础之上使用SSL／TLS，来保证client和sever之间的通过一个安全的SSL连接来完成通讯。主要的目的是两个：

* 保证通讯对象是我们想要的，简单来说就是保证当你访问https://www.google.com 时，你访问的是真正的google还不是一个黑客给你准备假网站。
* 保证通讯的内容的安全。

对于https来说，第一步就是一个https连接的建立，也就是我们常说的协议握手。https连接建立的握手主要是三个阶段 - 协议协商, 证书交换，Key交换。这里我们以访问https://example.com这样一个网站为例。

1. **协议协商** - 整个握手开始于client发送一个ClientHello的消息给server，这个消息里包含了client支持SSL的版本，以及cipher suites。当server收到这个消息后会回复给client一个server hello的消息，这里面会制定通讯需要使用SSL版本和cipher suite。
2. **证书交换** - 完成了第一步，接下来server要做提供相应的identity来证明自己就是真的https://example.com 。Server会返回client一个SSL的证书，而在这个证书会指明证书的拥有者，相应的域名，以及证书的公钥，电子签名以及证书有效时间。而在client，我们浏览器会根据我们系统预装一个CAs（Certificate Authorities)来检查证书的有效性。
3. **Key交换** - 整个https的通讯是通过[对称加密算法](https://en.wikipedia.org/wiki/Symmetric-key_algorithm)来完成的，这也就是说client和server端要统一使用一个key来对通讯进行加密。而对于[对称加密算法](https://en.wikipedia.org/wiki/Symmetric-key_algorithm)来说key就是关键，因为如果有人知道了你的key和加密使用的cipher suite就可以解密你的通讯内容。而在key交换这个环节，为了保证key交换的安全，https使用[非对称加密算法](https://en.wikipedia.org/wiki/Public-key_cryptography)。client会随机生成一个key，然后使用我们在第二步中拿到的证书中的公钥对key进行加密，然后发送给server。server会使用这个公钥对应的密钥解密然后拿到key。

完成了上面三步，https连接就算好了。接下来client和server会使用他们在握手阶段达成了一致，使用相应SSL和cipher suite以及key对发送的请求加密，接受端再使用他们对请求进行解密。

接下来，我们可以来看看整个连接建立过程中最重要的一个东西SSL证书。

本质上说SSL证书就是一个文本文件，任何人都可以打开并且修改，甚至创建一个。那么我们怎么来确认证书的有效性呢？

1. SSL证书的发放和申请都来自是由非常权威的CAs（Certificate Authorities)，在证书的申请过程中我们需要提供相应的证据来证明我们拥有example.com这个域名。
2. 对于client，我们的浏览器本身就已经预装了一些权威CAs，如果你的证书是由这些CAs发放的，我们浏览器上的CA就会检查证书上的电子签名。这个电子签名是信息是使用我们申请证书时候提供的私钥加密的，而这里浏览器上的CAs只需要使用证书中提供的公钥就行解密就可以验证电子签名的有效性了。由于私钥的保存非常安全，其他人是拿不到的，所以无法伪造这个签名。


切记，https并不是绝对安全的，依然会面临很多威胁。比如

* [protocol downgrade attack](https://en.wikipedia.org/wiki/Downgrade_attack)，我们需要[HSTS](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security)
* 如果我们在使用公司的网络时候，如果我们的计算机可以别人访问，他大可以在你的计算机中偷偷的安装他的CA，然后拦截你所有https的请求。
* ...


最后，使用https一定要注意证书的有效时间，防止证书过期。或者我们直接使用任何[ACME](https://en.wikipedia.org/wiki/Automated_Certificate_Management_Environment)的客户端，比如[Let's Encrypt](https://en.wikipedia.org/wiki/Let%27s_Encrypt)来帮助我们自动renew证书。
