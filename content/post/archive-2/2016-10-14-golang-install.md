---
title: golang install
author: wiloon
type: post
date: 2016-10-14T00:15:50+00:00
url: /?p=9295
categories:
  - Uncategorized

---
china mainland download
  
https://golang.google.cn/

<pre><code class="language-bash line-numbers">curl -O https://dl.google.com/go/go1.12.6.linux-amd64.tar.gz
</code></pre>

<pre><code class="language-bash line-numbers">export GOROOT=/root/go
export GOPATH=/root/gopath
export PATH=$GOROOT/bin:$GOPATH/bin:$PATH
export GOBIN=/path/to/go/bin

</code></pre>

GOPATH:

linux:
  
[shell]

mkdir -p /home/wiloon/my-projects/golang/lib/

mkdir -p /home/wiloon/my-projects/golang/projects/

export GOPATH=&#8221;/home/wiloon/my-projects/golang/lib/:/home/wiloon/my-projects/golang/projects/&#8221;

#check golang version
  
go version
  
[/shell]

windows:
  
GOPATH=C:\workspace\myproject\golang\lib;C:\workspace\myproject\golang\gox