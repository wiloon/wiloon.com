---
title: 'chromeos linux,  crostini'
author: wiloon
type: post
date: 2018-05-11T16:49:21+00:00
url: /?p=12236
categories:
  - Uncategorized

---
[code lang=shell]
  
vmc start dev
  
run\_container.sh &#8211;container\_name=stretch &#8211;user=wiloon &#8211;shell
  
[/code]

switch to dev channel, chrome os version >=67
  
chrome os will start to download an update
  
update and restart

Launch crosh (ctrl-alt-t)
  
Create crostini VM vmc start dev. This&#8217;ll download the termina component, and open a shell.
  
Launch a container run\_container.sh &#8211;container\_name=stretch &#8211;user=wiloon &#8211;shell

<https://www.youtube.com/watch?v=s9mrR2tqVbQ>

<blockquote class="reddit-card" >
  <p>
    <a href="https://www.reddit.com/r/Crostini/comments/89q1cu/crostini_101/?ref_source=embed&ref=share">Crostini 101</a> from <a href="https://www.reddit.com/r/Crostini/">Crostini</a>
  </p>
</blockquote>


  
https://github.com/lstoll/cros-crostini/blob/master/README.md
  
https://support.google.com/chromebook/answer/1086915?hl=en