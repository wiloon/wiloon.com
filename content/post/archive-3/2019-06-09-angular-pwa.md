---
title: angular pwa
author: wiloon
type: post
date: 2019-06-09T15:35:23+00:00
url: /?p=14484
categories:
  - Uncategorized

---
<pre><code class="line-numbers">ng new project0
ng add @angular/pwa --project project0
ng build --prod
cd project0 && http-server -p 8080 -c-1 dist/project0/

curl http://127.0.0.1:8080/ngsw/state
chrome://serviceworker-internals/
</code></pre>

<pre><code class="line-numbers">ng new my-app --routing
</code></pre>