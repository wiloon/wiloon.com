---
title: Java方法返回多个值
author: "-"
date: 2013-01-29T04:50:00+00:00
url: /?p=5083
categories:
  - Java
tags:
  - reprint
---
## Java方法返回多个值
  
     http://hi.baidu.com/xiaoyoue/item/7b905356a33d51948d12ed53
  


  我最初学java用的教材就是《java编程思想》，觉得这本书很多知识讲的很细、很透彻，但对一些知识点不理解，有些是没有读懂，有些是觉得这些知识点 与主要内容不相关。当时项目紧，对这些不理解的知识就过去了，反正已经可以编程了。做了2年的java开发之后，重读这本书的，才发现这些不相关知识的内 在联系，如果当时理会了作者的意图，工作中将会少走很多的弯路。

我重读第12章时，想起自己解决一个问题走了很多的弯路，而在这章都做了详细的讨论与说明，把这个问题拿出来，按我走过的弯路，每一步都讨论一下，希望初学者少走些弯路。

问题一: 在写程序的时候，希望某个方法有两个返回值，如何处理？

讨 论一: 用过C语言的人都知道，其中一个可以通过返回值返回，另一个可以通过一个指针参数返回 (将一个指针变量传入函数，改变指针指向的内容，达到目的) 。 Java没有指针，如何达到这个目的？12章的一段话说的很明白: "事实上，Java中每个对象 (除基本数剧类型以外) 的标识符都属于指针的一种。但它们 的使用受到了严格的限制和规范…"。是否可以利用这点返回多个值？回答是肯定的，java.io.InputStream 类的read方法就是这么用的，参见public int read(byte[] b)。

收获一: java方法的参数传递方法有两 种: 1、按值传递: java的基本数据类型，都是采用的这种方式；2、按引用传递，其它类型的对象，都是传递引用 (句柄) 。传递引用的方式有一个问题，即 两个引用指向同一个对象，在函数中修改了一个对象，同时也修改了函数外的那个对象，即容易引起别名的副作用。

结论一: 在一些情况下，可以利用别名的副作用，达到返回多个返回值的目的。如java.io.InputStream类的read方法。

问题二: 根据结论一，对于自定义的一些类，一般可以作为输出参数，达到返回值的目的，但对于基本数据类型，如何解决呢？

讨 论二: 既然每一个数据基本类型都对应一个包装类，是否可以使用相应的包装类，来达到目的呢？实践发现，这些包装类的对象是没有办法修改的，都是只读类。这 些类的对象，一旦创建，便不能修改，不能使用包装类达到目的。这种情况下，发现用数组可以达到目的，可以创建一个基本类型数据的数组，作为参数传递到方法 中，在方法中改变数组内容，达到目的。

收获二: 发现有些类是只读类，这些类的对象一旦创建，就不能修改；基本数据类型的包装类都是只读 类。这些类实际上使用了《java与 模式》一书中提到的不变模式。不变模式的优点: 1、不变的对象比可变对象更加容易维护；2、线程安全。缺点: 一旦需要修改一个不变的对象，就必须创建一个 新的对象。一些关于String和StringBuffer的区别的讨论，无非就是String采用的是不变模式，而StringBuffer没有采用不 变模式，本质上就是不变模式的讨论而已。

结论二: 对于基本数剧类型作为方法的输出参数的变通手段: 采用基本数据类型的数组可以达到目的，对于基本数据类型的包装类，则达不到目的。 (为了将一个基本数据类型作为输出参数就创建一个数组，只是达到目的的一种方式，不值得推荐使用。) 

问题三: 应用结论二，在RMI调用时，输出参数在方法调用时改变了，而在调用处却没有改变。为什么？

讨 论三: 我在写一个rmi服务接口时，采用以下的方法接口: byte[]  getFaxByte(Fax fax, int[] page)，其中 page作为输出参数，希望在调用时，函数改变page[0]的值，在函数调用过后，通过对page的引用得到修改后的值。在实践中我发现，rmi的服务 和调用在同一台机器上，可以得到正确结果，而rmi服务和调用不在同一台机器上，结果便不正确。

经过单步跟踪，发现了问题根源，原来是自己对rmi的原理不熟，没有理解rmi的基础。

收 获三: rmi是远程方法调用，本质上就是为了让一个JVM能够调用另外一个JVM的方法。Rmi的基本原理是利用stub和skel将远程对象伪装成自己 机器内的某个本地对象。容易忽略的一点: 远程接口的参数、返回参数必须实现Serializable接口；在远程调用时，将参数通过 Serializable传到远程的服务器上，而将远程的返回结果Serializable传回到调用的机器上。在这种情况下，由于参数在方法调用处和方 法的处理引用的不是同一个对象，因而便不会有结论一中提到的别名的副作用，在rmi调用，使用输出参数是不会成功的。

结论三: 在rmi中，不能使用输出参数，返回返回值。

问题四: 如何在rmi中返回多个参数？

讨论四: 我首先想到了Collection，Collection可以容纳多个对象，将返回值定义为Collection，可以在Collection中包含多个对象，作为返回值，这样就可以返回多个参数了。

结论四: 可以使用Collection实现在rmi调用中返回多个变量的目的。

问 题五: Collection容纳的对象只是Object类型，具体对象信息丢失。而且函数定义和函数调用必须对Collection内容纳的对象的次序有 一个良好的说明。也就是说，函数和调用处的耦合关系太强烈了，这种函数的可重用性太低，函数的使用者要非常了解这个函数，容易出现错误。

讨 论五: 结论四的编写程序的思想还是面向对象的一个思想，阅读了设计模式和重构方面的书之后，觉得自己的思路还是有些问题。一个方法如果需要返回两个以上的 返回值，这些返回值通常都有一定的关联性，存在关联性，就应该创建一个新的类，在类中包含需要返回的属性，将这个新创建的类作为返回值。

收获四: 在解决问题的过程中，不经意间就比较了一个简单问题用面向过程的思路和面向对象思路的差别，对OOP的某一方面有所体会，有助于个人编程思想由面向过程向OOP转变。

结论五: 如果函数需要返回多个返回值，尝试新创建一个类来解决问题。

问题六: 如果函数需要多个返回值，而各个返回值又都相互不关联，怎么处理？

讨论六: 会出现这种状况吗？重构一书有一个观点，一个方法应当只完成一个功能，当遇到问题六的状况时，应当检查方法的定义是否有问题？然后检查类的定义是否有问题？检查是否需要对代码重构？

我在刚刚开始用java的时候经常遇到需要返回多个返回值的问题，阅读过重构和设计模式的相关资料之后，基本上用结论五就可以解决，而出现问题六的可能性基本上没有了。虽问题六的仍然可以用结论四来解决，但我觉得从OOP的观点，还是应当检查一下自己的类设计的是否合理。

收获五: 体会到了重构给改善代码质量带来的好处，以后的工作中尝试使用重构。

结论六: 在编码过程中，发现一些用自己的知识比较难解决的编码问题，可以换一个思路检查一下初始的设计思路是否正确，使用重构改善代码质量。