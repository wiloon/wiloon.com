---
title: idea external tool
author: wiloon
type: post
date: 2017-07-28T01:58:23+00:00
url: /?p=10948
categories:
  - Uncategorized

---
https://intellij-support.jetbrains.com/hc/en-us/community/posts/206883315-Anyway-to-Make-IntelliJ-IDEA-run-command-line-commands

You can configure command line commands to execute via the &#8220;External Tools&#8221; feature. Go to File > Settings > [IDE Settings] > External Tools to create an External Tool definition. I&#8217;ve never done it with Subversion, but it should work. Just use the svn executable as the &#8220;Program&#8221; in the settings dialog. There are some pretty useful variables and macros you can define. See the Help for more information. I&#8217;ve used this feature to run SCP commands among other things.

<pre><code class="language-bash line-numbers">vim /path/to/maven-clean-install.sh

#!/bin/bash
mvn clean install -Dmaven.test.skip=true -U

chmod u+x /path/to/maven-clean-install.sh
</code></pre>

Go to File > Settings > [IDE Settings] > External
  
Add
          
Name: mvn clean install
          
Group: maven
          
Tool Settings
                  
Program: /path/to/maven-clean-install.sh
                  
Working directory: $FileDir$