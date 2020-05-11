---
title: Class.forName
author: wiloon
type: post
date: 2013-12-21T13:11:52+00:00
url: /?p=6067
categories:
  - Uncategorized
tags:
  - Java

---
Class.forName(xxx.xx.xx) 返回的是一个类

首先你要明白在java里面任何class都要装载在虚拟机上才能运行。这句话就是装载类用的(和new 不一样，要分清楚)。

至于什么时候用，你可以考虑一下这个问题，给你一个字符串变量，它代表一个类的包名和类名，你怎么实例化它？只有你提到的这个方法了，不过要再加一点。
  
A a = (A)Class.forName(&#8220;pacage.A&#8221;).newInstance();
  
这和你
  
A a = new A()；
  
是一样的效果。

关于补充的问题
  
答案是肯定的，jvm会执行静态代码段，你要记住一个概念，静态代码是和class绑定的，class装载成功就表示执行了你的静态代码了。而且以后不会再走这段静态代码了。

Class.forName(xxx.xx.xx) 返回的是一个类
  
Class.forName(xxx.xx.xx);的作用是要求JVM查找并加载指定的类，也就是说JVM会执行该类的静态代码段

动态加载和创建Class 对象，比如想根据用户输入的字符串来创建对象
  
String str = 用户输入的字符串
  
Class t = Class.forName(str);
  
t.newInstance();

在初始化一个类，生成一个实例的时候，newInstance()方法和new关键字除了一个是方法，一个是关键字外，最主要有什么区别？它们的区别在于创建对象的方式不一样，前者是使用类加载机制，后者是创建一个新类。那么为什么会有两种创建对象方式？这主要考虑到软件的可伸缩、可扩展和可重用等软件设计思想。

Java中工厂模式经常使用newInstance()方法来创建对象，因此从为什么要使用工厂模式上可以找到具体答案。 例如：
  
class c = Class.forName(“Example”);
  
factory = (ExampleInterface)c.newInstance();

其中ExampleInterface是Example的接口，可以写成如下形式：
  
String className = &#8220;Example&#8221;;
  
class c = Class.forName(className);
  
factory = (ExampleInterface)c.newInstance();

进一步可以写成如下形式：
  
String className = readfromXMlConfig;//从xml 配置文件中获得字符串
  
class c = Class.forName(className);
  
factory = (ExampleInterface)c.newInstance();

上面代码已经不存在Example的类名称，它的优点是，无论Example类怎么变化，上述代码不变，甚至可以更换Example的兄弟类Example2 , Example3 , Example4……，只要他们继承ExampleInterface就可以。

从JVM的角度看，我们使用关键字new创建一个类的时候，这个类可以没有被加载。但是使用newInstance()方法的时候，就必须保证：1、这个类已经加载；2、这个类已经连接了。而完成上面两个步骤的正是Class的静态方法forName()所完成的，这个静态方法调用了启动类加载器，即加载java API的那个加载器。

现在可以看出，newInstance()实际上是把new这个方式分解为两步，即首先调用Class加载方法加载某个类，然后实例化。 这样分步的好处是显而易见的。我们可以在调用class的静态加载方法forName时获得更好的灵活性，提供给了一种降耦的手段。

最后用最简单的描述来区分new关键字和newInstance()方法的区别：
  
newInstance: 弱类型。低效率。只能调用无参构造。
  
new: 强类型。相对高效。能调用任何public构造。

Java的反射机制

每个Java程序执行前都必须经过编译、加载、连接、和初始化这几个阶段，后三个阶段如下图：

其中

Java中反射机制和Class.forName、实例对象.class(属性)、实例对象getClass()的区别

i、加载是指将编译后的java类文件（也就是.class文件）中的二进制数据读入内存，并将其放在运行时数据区的方法区内，然后再堆区创建一个Java.lang.Class对象，用来封装类在方法区的数据结构。即加载后最终得到的是Class对象，并且更加值得注意的是：该Java.lang.Class对象是单实例的，无论这个类创建了多少个对象，他的Class对象时唯一的！！！！。 而 加载并获取该Class对象可以通过三种途径：

Class.forName（类的全路径）、实例对象.class(属性)、实例对象getClass()。关于他们的区别将在下面讲到！！！

###另外 ，类加载时类中的静态代码块会得到执行（详见前一篇博客：Class.forName()加载JDBC驱动）

ii、在 连接和初始化阶段，其实静态变量经过了两次赋值：第一次是静态变量类型的默认值；第二次是我们真正赋给静态变量的值。

iii、Java对类的使用分为两种方式：主动使用和被动使用。其中主动使用如下图：

Java中反射机制和Class.forName、实例对象.class(属性)、实例对象getClass()的区别而类的初始化时机正是java程序对类的首次主动使用，除了以上6中方式，其他对类的使用都是被动使用，都不会导致类的初始化。 并且应该注意以下几个方面：

Java中反射机制和Class.forName、实例对象.class(属性)、实例对象getClass()的区别

Java中反射机制和Class.forName、实例对象.class(属性)、实例对象getClass()的区别在这里可以看出;接口的两重性：可以把接口当做类（因为在接口中有静态变量时，他可以被初始化）；接口就是接口，和类无关（接口中 没有构造方法，所以不能被初始化）

二、Class.forName、实例对象.class(属性)、实例对象getClass()的区别

1、相同点：

通过这几种方式，得到的都是Java.lang.Class对象（这个是上面讲到的 类在加载时获得的最终产物）

例如：

[java]

package demo;

public class A

{

public static void main(String[] args) throws Exception

{

System.out.println(A.class);//通过类.class获得Class对象

&nbsp;

A a = new A();

System.out.println(a.getClass());//通过 实例名.getClass()获得Class对象

System.out.println(Class.forName("demo.A"));//通过Class.forName(全路径)获得Class对象

System.out.println("&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;");

System.out.println(a);//使用不同的方式创建对象

System.out.println(A.class.newInstance());

System.out.println(a.getClass().newInstance());

System.out.println(Class.forName("demo.A").newInstance());

}

}

[/java]

结果：class demo.A

class demo.A

class demo.A （这里也可以得到一个类的Class对象是唯一的）

&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;&#8230;

demo.A@de6ced

demo.A@c17164

demo.A@1fb8ee3

demo.A@61de33

2、区别：

1)Class cl=A.class; JVM将使用类A的类装载器,将类A装入内存(前提是:类A还没有装入内存),不对类A做类的初始化工作.返回类A的Class的对象

2)Class cl=对象引用o.getClass();返回引用o运行时真正所指的对象(因为:儿子对象的引用可能会赋给父对象的引用变量中)所属的类的Class的对象

3)Class.forName(&#8220;类名&#8221;); JAVA人都知道.装入类A,并做类的初始化

附：

从JVM的角度看，我们使用关键字new创建一个类的时候，这个类可以没有被加载。但是使用Class对象的newInstance()方法的时候，就必须保证：1、这个 类已经加载；2、这个类已经连接了。而完成上面两个步骤的正是Class的静态方法forName()所完成的，这个静态方法调用了启动类加载器，即加载 java API的那个加载器。

现在可以看出，Class对象的newInstance()（这种用法和Java中的工厂模式有着异曲同工之妙）实际上是把new这个方式分解为两步，即首先调用Class加载方法加载某个类，然后实例化。 这样分步的好处是显而易见的。我们可以在调用class的静态加载方法forName时获得更好的灵活性，提供给了一种降耦的手段。

最后用最简单的描述来区分new关键字和newInstance()方法的区别：

newInstance: 弱类型。低效率。只能调用无参构造。

new: 强类型。相对高效。能调用任何public构造。

&nbsp;

&nbsp;

&nbsp;

Class.forName（String driverClassName）加载JDBC驱动程序时，底层都做了些什么？？？

实质是：

&nbsp;

&nbsp;

&nbsp;

Class.forName（“com.mysql.jdbc.Driver”）是 强制JVM将com.mysql.jdbc.Driver这个类加载入内存，并将其注册到DriverManager类，然后根据DriverManager.getConnection（url，user,pwd）中的url找到相应的驱动类，最后调用该该驱动类的connect(url, info)来获得connection对象。

&nbsp;

&nbsp;

&nbsp;

JDBC的驱动管理机制的 具体底层代码分析如下：

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

1. 分析JDBC的驱动程序管理部分的实现代码：

&nbsp;

在 JDBC的层次上，sun主要定义了1个接口Driver和两个类：DirverManager和DriverInfo。每个JDBC驱动程序必须实现 Driver接口（在MySql的Connector/J驱动中，这个叫做com.mysql.jdbc.Driver）。而DriverManager 则负责管理所有的Driver对象，包含注册Driver；选择合适的Driver来建立到某个数据库的连接；以及进行一些Driver的信息管理等。 DriverInfo非常简单，用于保存Driver的信息，只有3个成员变量，Driver，DriverClass和 DriverClassName，意义非常明显。

&nbsp;

先看一下在DriverManager.java中的关键代码：

private static java.util.Vector drivers = new java.util.Vector();

所有的Driver对象保存在一个Vector数组中。

&nbsp;

注册Driver的函数叫registerDriver，将需要注册的Driver对象传入即可：

&nbsp;

&nbsp;

复制代码

public static synchronized void registerDriver(java.sql.Driver driver)

throws SQLException {

if (!initialized) { //如果没有初始化，则先初始化

initialize();

}

DriverInfo di = new DriverInfo(); // 实际保存的不是Driver，而是一个DriverInfo对象，但是DriverInfo的其它成员完全可以由Driver推导出来，所以个人觉得 DriverInfo对象可有可无，直接使用Driver应该就可以了。

di.driver = driver;

di.driverClass = driver.getClass();

di.driverClassName = di.driverClass.getName();

drivers.addElement(di); //将DriverInfo对象添加到数组中

println(&#8220;registerDriver: &#8221; + di);

}

复制代码

&nbsp;

&nbsp;

&nbsp;

&nbsp;

在一个类加载入内存的时候，类中的静态初始化过程会执行，这样就完成了驱动程序的注册过程。然后重点看一下建立数据库连接的代码，在getConnection函数中，省略了一些非关键代码：

&nbsp;

&nbsp;

复制代码

private static synchronized Connection getConnection(

String url, java.util.Properties info, ClassLoader callerCL) throws SQLException {

&nbsp;

SQLException reason = null;

//轮询所有的DriverInfo对象。

for (int i = 0; i < drivers.size(); i++) {

DriverInfo di = (DriverInfo)drivers.elementAt(i);

try {

//使用DriverInfo中的Driver对象去做实际的连接数据库的工作

Connection result = di.driver.connect(url, info);

if (result != null) {

// Success!

println(&#8220;getConnection returning &#8221; +&bsp;di);

return (result); //一旦成功连接，直接返回Connection对象，然后推出

}

} catch (SQLException ex) {

&#8230;

}

}

//这就是经常看到的出错信息－－找不到合适的驱动程序。

println(&#8220;getConnection: no suitable driver&#8221;);

throw new SQLException(&#8220;No suitable driver&#8221;, &#8220;08001&#8221;);

}

复制代码

&nbsp;

&nbsp;

&nbsp;

由 上面的getConnection函数可以看到，真正实现数据库连接的是Driver对象的connect函数。而且可以看到，由于 DriverManager.getConnection使用的是一种轮询的方式，注册的驱动程序越多，连接速度会越慢。JDBC连接数据库的速度很慢， 是不是和这种实现方式有关联呢？怀着这个问题，本人下载了MySql的Connector/J驱动包，开始分析其connect函数的实现。

&nbsp;

2. 分析MySql的注册和建立连接部分的代码：

打开MySql的源码包，首先分析其Driver类的实现。发现Driver类的实现非常简单，

&nbsp;

&nbsp;

复制代码

public class Driver extends NonRegisteringDriver implements java.sql.Driver {

static {

try {

java.sql.DriverManager.registerDriver(new Driver());

} catch (SQLException E) {

throw new RuntimeException(&#8220;Can&#8217;t register driver!&#8221;);

}

}

public Driver() throws SQLException {

// Required for Class.forName().newInstance()

}

复制代码

&nbsp;

&nbsp;

&nbsp;

可 以看到，有一段static代码，调用了DriverManager的registerDriver方法。这其实就解释了 Class.forName（“com.mysql.jdbc.Driver”）能够完成MySql驱动注册的问题。因为forName会导致这段 static代码被调用，从而间接调用了registerDriver，完成注册过程。

&nbsp;

com.mysql.jdbc.Driver 从com.mysql.jdbc.NonRegisteringDriver继承而来，实际上是NonReisteringDriver完成了 java.sql.Driver接口的实现工作。转移目标，分析NonRegisteringDriver的connect函数。

&nbsp;

NonRegisteringDriver.connect的实现也比较简单，正合我意：

&nbsp;

public java.sql.Connection connect(String url, Properties info)

throws SQLException {

&nbsp;

//1. 分析传入的连接字符串.

&nbsp;

&nbsp;

复制代码

if ((props = parseURL(url, info)) == null) {

return null;

}

try {

//2. 建立一个Connection对象完成实际的数据库连接工作

Connection newConn = new com.mysql.jdbc.Connection(host(props),

port(props), props, database(props), url, this);

return newConn;

&nbsp;

}

复制代码

&nbsp;

&nbsp;

非 常简单，先parseURL，然后使用Connection去建立连接。parseURL只是简单的字符串分析，主要是分析传入的连接字符串是否满足 “jdbc:mysql://host:port/database“的格式，如果不满足，直接返回null，然后由DriverManager去试验下 一个Driver。如果满足，则建立一个Connection对象建立实际的数据库连接，这不是本人关注的问题，源码分析就此打住。

&nbsp;

由此可见1中的问题答案是：DriverManager的轮询查询注册的Driver对象的工作方式所带来的性能代价并不是很大，主工作量只是parseURL函数。

&nbsp;

&nbsp;

博客园：www.cnblogs.com/liuxianan

微博：weibo.com/liuxianan

QQ:937925941

Copyright ©2013 六仙庵

<http://blog.csdn.net/ouyangmeile/article/details/3889797>

<http://blog.sina.com.cn/s/blog_7ffb8dd5010127ix.html>

<http://www.cnblogs.com/liuxianan/archive/2012/08/04/2623258.html>