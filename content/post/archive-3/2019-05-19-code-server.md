---
title: code-server
author: wiloon
type: post
date: 2019-05-19T01:52:15+00:00
url: /?p=14361
categories:
  - Uncategorized

---
<pre><code class="language-bash line-numbers">docker run -d \
--name code-server \
-p 38443:8443 \
-v "code-server-project:/home/coder/project" \
codercom/code-server \
--allow-http --no-auth
</code></pre>

https://github.com/cdr/code-server
  
https://hub.docker.com/r/codercom/code-server