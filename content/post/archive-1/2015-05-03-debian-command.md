---
title: debian command
author: wiloon
type: post
date: 2015-05-03T13:10:25+00:00
url: /?p=7589
categories:
  - Uncategorized
tags:
  - linux

---
<pre><code class="language-bash line-numbers"># list installed package
dpkg --list

# remove package
apt-get remove foo

# ping, command not found
apt-get install inetutils-ping


</code></pre>

dpkg-reconfigure tzdata