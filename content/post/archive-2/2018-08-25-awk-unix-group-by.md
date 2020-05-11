---
title: Awk/Unix group by
author: wiloon
type: post
date: 2018-08-25T10:00:43+00:00
url: /?p=12565
categories:
  - Uncategorized

---
[code lang=shell]
  
$ awk -F, 'NR>1{arr[$1]++}END{for (a in arr) print a, arr[a]}' file.txt
  
[/code]

joe 1
  
jim 1
  
mike 3
  
bob 2
  
EXPLANATIONS
  
-F, splits on ,
  
NR>1 treat lines after line 1
  
arr[$1]++ increment array arr (split with ,) with first column as key
  
END{} block is executed @ the end of processing the file
  
for (a in arr) iterating over arr with a key
  
print a print key , arr[a] array with a key

https://stackoverflow.com/questions/14916826/awk-unix-group-by