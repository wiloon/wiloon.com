---
title: junit performance test
author: wiloon
type: post
date: 2011-09-06T09:30:28+00:00
url: /?p=684
bot_views:
  - 6
categories:
  - Uncategorized

---
http://databene.org/contiperf.html
          
@Rule
	  
public ContiPerfRule i = new ContiPerfRule();

@Test
	  
@PerfTest(invocations = 5)
	  
@Required(max = 9000, average = 8000)

invocation sequence:
  
constructor()
  
before()
  
test1()
  
test1()
  
after()
  
constructor()
  
before()
  
test2()
  
test2()
  
after()