---
title: AOP
author: "-"
date: 2012-01-07T07:58:58+00:00
url: /?p=2097
categories:
  - Java
tags:
  - reprint
---
## AOP

AOP为Aspect Oriented Programming的缩写，意为: 面向切面编程 (也叫面向方面) ，可以通过预编译方式和运行期动态代理实现在不修改源代码的情况下给程序动态统一添加功能的一种技术。AOP实际是GoF设计模式的延续，设计模式孜孜不倦追求的是调用者和被调用者之间的解耦，AOP可以说也是这种目标的一种实现。
  
为什么要区分J2EE容器和J2EE应用系统？
  
我们知道，J2EE应用系统只有部署在J2EE容器中才能运行，那么为什么划分为J2EE容器和J2EE应用系统？ 通过对J2EE容器运行机制的分析 (见我的电子教材"EJB实用原理") ，我们可以发现: 实际上J2EE容器分离了一般应用系统的一些通用功能，例如事务机制、安全机制以及对象池或线程池等性能优化机制。

这些功能机制是每个应用系统几乎都需要的，因此可以从具体应用系统中分离出来，形成一个通用的框架平台，而且，这些功能机制的设计开发有一定难度，同时运行的稳定性和快速性都非常重要，必须经过长时间调试和运行经验积累而成，因此，形成了专门的J2EE容器服务器产品，如Tomcat JBoss、Websphere、WebLogic等。

从J2EE系统划分为J2EE容器和J2EE应用系统两个方面，我们已经看到一种分散关注的思路 (separation of concerns) 。

**分散关注**

将通用需求功能从不相关类之中分离出来；同时，能够使得很多类共享一个行为，一旦行为发生变化，不必修改很多类，只要修改这个行为就可以。

AOP就是这种实现分散关注的编程方法，它将"关注"封装在"方面"中。

**AOP是什么？**

AOP是OOP的延续，是Aspect Oriented Programming的缩写，意思是面向方面编程。AOP实际是GoF设计模式的延续，设计模式孜孜不倦追求的是调用者和被调用者之间的解耦，AOP可以说也是这种目标的一种实现。

举例: 假设有在一个应用系统中，有一个共享的数据必须被并发同时访问，首先，将这个数据封装在数据对象中，称为Data Class，同时，将有多个访问类，专门用于在同一时刻访问这同一个数据对象。

为了完成上述并发访问同一资源的功能，需要引入锁Lock的概念，也就是说，某个时刻，当有一个访问类访问这个数据对象时，这个数据对象必须上锁Locked，用完后就立即解锁unLocked，再供其它访问类访问。

使用传统的编程习惯，我们会创建一个抽象类，所有的访问类继承这个抽象父类，如下:

```java
  
abstract class Worker{

abstract void locked();
  
abstract void accessDataObject();
  
abstract void unlocked();

}
  
```

缺点:

* accessDataObject()方法需要有"锁"状态之类的相关代码。
* Java只提供了单继承，因此具体访问类只能继承这个父类，如果具体访问类还要继承其它父类，比如另外一个如Worker的父类，将无法方便实现。
* 重用被打折扣，具体访问类因为也包含"锁"状态之类的相关代码，只能被重用在相关有"锁"的场合，重用范围很窄。仔细研究这个应用的"锁"，它其实有下列特性:
* "锁"功能不是具体访问类的首要或主要功能，访问类主要功能是访问数据对象，例如读取数据或更改动作。
* "锁"行为其实是和具体访问类的主要功能可以独立、区分开来的。
* "锁"功能其实是这个系统的一个纵向切面，涉及许多类、许多类的方法。因此，一个新的程序结构应该是关注系统的纵向切面，例如这个应用的"锁"功能，这个新的程序结构就是aspect (方面) 在这个应用中，"锁"方面 (aspect) 应该有以下职责:  
    提供一些必备的功能，对被访问对象实现加锁或解锁功能。以保证所有在修改数据对象的操作之前能够调用lock()加锁，在它使用完成后，调用unlock()解锁。

    **AOP应用范围**

    很明显，AOP非常适合开发J2EE容器服务器，目前JBoss 4.0正是使用AOP框架进行开发。
  
    具体功能如下:
  
    Authentication 权限
  
    Caching 缓存
  
    Context passing 内容传递
  
    Error handling 错误处理
  
    Lazy loading懒加载
  
    Debugging调试
  
    logging, tracing, profiling and monitoring记录跟踪优化校准
  
    Performance optimization性能优化
  
    Persistence持久化
  
    Resource pooling资源池
  
    Synchronization同步
  
    Transactions 事务

    **AOP有必要吗？**

    当然，上述应用范例在没有使用AOP情况下，也得到了解决，例如JBoss 3.XXX也提供了上述应用功能，但是没有使用AOP。

    但是，使用AOP可以让我们从一个更高的抽象概念来理解软件系统，AOP也许提供一种有价值的工具。可以这么说: 因为使用AOP结构，现在JBoss 4.0的源码要比JBoss 3.X容易理解多了，这对于一个大型复杂系统来说是非常重要的。

    从另外一个方面说，好像不是所有的人都需要关心AOP，它可能是一种架构设计的选择，如果选择J2EE系统，AOP关注的上述通用方面都已经被J2EE容器实现了，J2EE应用系统开发者可能需要更多地关注行业应用方面aspect。
  
    **AOP具体实现**

    AOP是一个概念，并没有设定具体语言的实现，它能克服那些只有单继承特性语言的缺点 (如Java) ，目前AOP具体实现有以下几个项目:

    AspectJ (TM): 创建于Xerox PARC. 有近十年历史，成熟
  
    缺点: 过于复杂；破坏封装；需要专门的Java编译器。

    动态AOP: 使用JDK的动态代理API或字节码Bytecode处理技术。

    基于动态代理API的具体项目有:
  
    JBoss 4.0 JBoss 4.0服务器
  
    nanning这是以中国南宁命名的一个项目，搞不清楚为什么和中国相关？是中国人发起的？
  
    JdonFramework Jdon自己的IoC AOP框架 粗粒度AOP

    基于字节码的项目有:
  
    aspectwerkz
  
    spring  

    板桥里人 [http://www.jdon.com](http://www.jdon.com) 2004/01/09

    [http://baike.baidu.com.cn/view/73626.htm](http://baike.baidu.com.cn/view/73626.htm)
