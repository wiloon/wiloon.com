---
title: chromeos Secure Shell App remove known host
author: wiloon
type: post
date: 2020-03-14T06:27:26+00:00
url: /?p=15741
categories:
  - Uncategorized

---
https://medium.com/code-kings/secure-shell-removing-known-host-by-index-when-changing-ip-address-18ed4161763c

打开 Secure Shell App
  
打开开发者工具 Ctrl+Shift+J
  
在Console中执行

<pre><code class="language-bash line-numbers">term_.command.removeKnownHostByIndex(YOUR_INDEX_NUMBER_HERE);
</code></pre>