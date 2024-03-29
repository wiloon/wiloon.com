---
title: 正向代理, Forward Proxy, 反向代理, reverse proxy
author: "-"
date: 2017-10-26T05:17:39+00:00
url: /?p=11305
categories:
  - Inbox
tags:
  - reprint
---
## 正向代理, Forward Proxy, 反向代理, reverse proxy

[![pij5ran.png](https://s11.ax1x.com/2024/01/03/pij5ran.png)](https://imgse.com/i/pij5ran)

正向代理是代理客户端, 为客户端收发请求, 使真实客户端对服务器不可见；
而反向代理是代理服务器端, 为服务器收发请求, 使真实服务器对客户端不可见。

### 正向代理, Forward Proxy

正向代理是一个位于客户端和目标服务器之间的代理服务器(中间服务器)。为了从原始服务器取得内容,客户端向代理服务器发送一个请求,并且指定目标服务器,之后代理向目标服务器转交并且将获得的内容返回给客户端。正向代理的情况下客户端必须要进行一些特别的设置才能使用。

### 反向代理, reverse proxy

反向代理正好相反。对于客户端来说,反向代理就好像目标服务器。并且客户端不需要进行任何设置。客户端向反向代理发送请求,接着反向代理判断请求走向何处,并将请求转交给客户端,使得这些内容就好似他自己一样,一次客户端并不会感知到反向代理后面的服务,也因此不需要客户端做任何设置,只需要把反向代理服务器当成真正的服务器就好了。

区别

正向代理需要你主动设置代理服务器ip或者域名进行访问,由设置的服务器ip或者域名去获取访问内容并返回；而反向代理不需要你做任何设置,直接访问服务器真实ip或者域名,但是服务器内部会自动根据访问内容进行跳转及内容返回,你不知道它最终访问的是哪些机器。

作者: Jovi_Mac
  
链接: [http://www.jianshu.com/p/208c02c9dd1d](http://www.jianshu.com/p/208c02c9dd1d)
  
來源: 简书
  
著作权归作者所有。商业转载请联系作者获得授权,非商业转载请注明出处。

[https://github.com/moonbingbing/openresty-best-practices/blob/master/ngx/reverse_proxy.md](https://github.com/moonbingbing/openresty-best-practices/blob/master/ngx/reverse_proxy.md)

什么是反向代理

反向代理 (Reverse Proxy) 方式是指用代理服务器来接受 internet 上的连接请求,然后将请求转发给内部网络上的服务器,并将从服务器上得到的结果返回给 internet 上请求连接的客户端,此时代理服务器对外就表现为一个反向代理服务器。

举个例子,一个用户访问 [http://www.example.com/readme](http://www.example.com/readme),但是 www.example.com 上并不存在 readme 页面,它是偷偷从另外一台服务器上取回来,然后作为自己的内容返回给用户。但是用户并不知情这个过程。对用户来说,就像是直接从 www.example.com 获取 readme 页面一样。这里所提到的 www.example.com 这个域名对应的服务器就设置了反向代理功能。

反向代理服务器,对于客户端而言它就像是原始服务器,并且客户端不需要进行任何特别的设置。客户端向反向代理的命名空间(name-space)中的内容发送普通请求,接着反向代理将判断向何处(原始服务器)转交请求,并将获得的内容返回给客户端,就像这些内容原本就是它自己的一样。
  
反向代理典型应用场景

反向代理的典型用途是将防火墙后面的服务器提供给 Internet 用户访问,加强安全防护。反向代理还可以为后端的多台服务器提供负载均衡,或为后端较慢的服务器提供 缓冲 服务。另外,反向代理还可以启用高级 URL 策略和管理技术,从而使处于不同 web 服务器系统的 web 页面同时存在于同一个 URL 空间下。

Nginx 的其中一个用途是做 HTTP 反向代理,下面简单介绍 Nginx 作为反向代理服务器的方法。

场景描述: 访问本地服务器上的 README.md 文件 <http://localhost/README.md>,本地服务器进行反向代理,从 [https://github.com/moonbingbing/openresty-best-practices/blob/master/README.md](https://github.com/moonbingbing/openresty-best-practices/blob/master/README.md) 获取页面内容。

[http://www.jianshu.com/p/208c02c9dd1d](http://www.jianshu.com/p/208c02c9dd1d)
  
[https://www.tuicool.com/articles/M7bAnqy](https://www.tuicool.com/articles/M7bAnqy)

非透明代理：客户端需要修改目的地址为代理服务器的地址，并使用代理协议连接代理服务器；

透明代理：所谓透明代理，即客户端和服务端感知不到代理的存在，客户端无需修改目的地址，也不需要采用代理协议连接代理服务器，所有目的地址转换都是在透明代理中完成的；
