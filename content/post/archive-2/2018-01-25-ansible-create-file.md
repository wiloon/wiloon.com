---
title: ansible create file
author: wiloon
type: post
date: 2018-01-25T04:42:29+00:00
url: /?p=11786
categories:
  - Uncategorized

---
[code lang=shell]
  
&#8211; name: ensure file exists
    
copy:
      
content: ""
      
dest: /etc/nologin
      
force: no
      
group: sys
      
owner: root
      
mode: 0555
  
[/code]

https://stackoverflow.com/questions/28347717/how-to-create-an-empty-file-with-ansible