---
title: java import cert
author: wiloon
type: post
date: 2018-08-03T02:55:02+00:00
url: /?p=12484
categories:
  - Uncategorized

---
<pre><code class="language-bash line-numbers">keytool -importcert -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -file /path/to/ca.der -alias "digicertglobalrootca"
# storepass 默认值changeit
keytool -list -keystore $JAVA_HOME/jre/lib/security/cacerts -storepass changeit | grep digicertglobalrootca

</code></pre>