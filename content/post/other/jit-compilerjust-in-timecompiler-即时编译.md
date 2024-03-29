---
title: 'JAVA   JIT Compiler(Just-in-timeCompiler) 即时编译'
author: "-"
date: 2015-02-05T02:41:46+00:00
url: /?p=7308
categories:
  - Inbox
tags:
  - Java

---
## 'JAVA   JIT Compiler(Just-in-timeCompiler) 即时编译'

JIT Compiler(Just-in-timeCompiler) 即时编译
  
最早的Java建置方案是由一套转译程式 (interpreter) ,将每个Java指令都转译成对等的微处理器指令,并根据转译后的指令先后次序依序执行,由于一个Java指令可能被转译成十几或数十几个对等的微处理器指令,这种模式执行的速度相当缓慢。
  
针对这个问题,业界首先开发出JIT (just in time) 编译器。当Java执行runtime环境时,每遇到一个新的类别 (class: 类别是Java程式中的功能群组) ,JIT编译器在此时就会针对这个类别进行编译 (compile) 作业。经过编译后的程式,被优化成相当精简的原生型指令码 (native code) ,这种程式的执行速度相当快。花费少许的编译时间来节省稍后相当长的执行时间,JIT这种设计的确增加不少效率,但是它并未达到最顶尖的效能,因为某些极少执行到的Java指令在编译时所额外花费的时间可能比转译器在执行时的时间还长,针对这些指令而言,整体花费的时间并没有减少。
  
基于对JIT的经验,业界发展出动态编译器 (dynamiccompiler) ,动态编译器仅针对较常被执行的程式码进行编译,其余部分仍使用转译程式来执行。也就是说,动态编译器会研判是否要编译每个类别。动态编译器拥有两项利器: 一是转译器,另一则是JIT,它透过智慧机制针对每个类别进行分析,然后决定使用这两种利器的哪一种来达到最佳化的效果。动态编译器针对程式的特性或者是让程式执行几个循环,再根据结果决定是否编译这段程式码。这个决定不见得绝对正确,但从统计数字来看,这个判断的机制正确的机会相当高。事实上,动态编译器会根据「历史资料」做决策,所以程式执行的时间愈长,判断正确的机率就愈高。以整个结果来看,动态编译器产生的程式码执行的速度超越以前的JIT技术,平均速度可提高至50%。

[http://baike.baidu.com/view/132440.htm?fromtitle=jit&fromid=2039740&type=syn](http://baike.baidu.com/view/132440.htm?fromtitle=jit&fromid=2039740&type=syn)
