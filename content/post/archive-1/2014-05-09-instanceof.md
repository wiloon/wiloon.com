---
title: instanceof
author: wiloon
type: post
date: 2014-05-09T07:07:58+00:00
url: /?p=6611
categories:
  - Uncategorized
tags:
  - Java

---
Java中的instanceof关键字

instanceof是Java的一个二元操作符，和==，>，<是同一类东东。由于它是由字母组成的，所以也是Java的保留关键字。它的作用是测试它左边的对象是否是它右边的类的实例，返回boolean类型的数据。举个例子：

String s = &#8220;I AM an Object!&#8221;;
  
boolean isObject = s instanceof Object;

我们声明了一个String对象引用，指向一个String对象，然后用instancof来测试它所指向的对象是否是Object类的一个实例，显然，这是真的，所以返回true，也就是isObject的值为True。
  
instanceof有一些用处。比如我们写了一个处理账单的系统，其中有这样三个类：

public class Bill {//省略细节}
  
public class PhoneBill extends Bill {//省略细节}
  
public class GasBill extends Bill {//省略细节}

在处理程序里有一个方法，接受一个Bill类型的对象，计算金额。假设两种账单计算方法不同，而传入的Bill对象可能是两种中的任何一种，所以要用instanceof来判断：

public double calculate(Bill bill) {
  
if (bill instanceof PhoneBill) {
  
//计算电话账单
  
}
  
if (bill instanceof GasBill) {
  
//计算燃气账单
  
}
  
&#8230;
  
}
  
这样就可以用一个方法处理两种子类。

然而，这种做法通常被认为是没有好好利用面向对象中的多态性。其实上面的功能要求用方法重载完全可以实现，这是面向对象变成应有的做法，避免回到结构化编程模式。只要提供两个名字和返回值都相同，接受参数类型不同的方法就可以了：

public double calculate(PhoneBill bill) {
  
//计算电话账单
  
}

public double calculate(GasBill bill) {
  
//计算燃气账单
  
}

所以，使用instanceof在绝大多数情况下并不是推荐的做法，应当好好利用多态。

&nbsp;

http://rodney.cnblogs.com/archive/2005/08/18/instanceof.html

10.4 instanceof判断对象类型
  
2008-10-11 21:37 李钟尉/马文强/陈丹丹 清华大学出版社 字号：T | T
  
一键收藏，随时查看，分享好友！
  
《Java从入门到精通》第10章接口、继承与多态，继承和多态是面向对象开发语言中非常重要的一个环节，如果在程序中使用得当，可以将整个程序的架构变得非常有弹性，同时可以减少代码的冗余性。本小节为大家介绍的是instanceof判断对象类型。
  
AD：51CTO学院：IT精品课程在线看！
  
10.4 instanceof判断对象类型

教学录像：光盘\TM\lx\10\ instanceof判断对象类型.exe

当在程序中执行向下转型操作时，如果父类对象不是子类对象的实例，就会发生ClassCastException异常，所以在执行向下转型之前需要养成一个良好习惯，就是判断父类对象是否为子类对象的实例。这个判断通常使用instanceof操作符来完成。可以使用instanceof操作符判断是否一个类实现了某个接口（接口会在10.6节中进行介绍），也可以用它来判断一个实例对象是否属于一个类。

instanceof的语法格式如下：

myobject instanceof ExampleClass
  
myobject：某类的对象引用。

ExampleClass：某个类。

使用instanceof操作符的表达式返回值为布尔值。如果使用instanceof操作符的表达式返回值为true，说明myobject对象为ExampleClass的实例对象，如果返回值为false，说明myobject对象不是ExampleClass的实例对象。

注意：instanceof是Java语言的关键字，在Java语言中的关键字都为小写。

下面来看一个向下转型与instanceof操作符结合的例子。

例10.7 在项目中创建Parallelogram类和3个内部类Quadrangle、Square、Anything。其中Parallelogram类和Square继承Quadrangle类，在Parallelogram类的主方法中分别创建这些类的对象，然后使用instanceof操作符判断它们的类型并输出结果。（实例位置：光盘\TM\sl\10\6）

class Quadrangle{
  
public static void draw(Quadrangle q){
  
//SomeSentence
  
}
  
}
  
class Square extends Quadrangle{
  
//SomeSentence
  
}
  
class Anything {
  
// SomeSentence
  
}
  
public class Parallelogram extends Quadrangle{
  
public static void main(String args[]){
  
Quadrangle q=new Quadrangle(); //实例化父类对象
  
if(q instanceof Parallelogram){ //判断父类对象是否为Parallelogram子类的一个实例
  
Parallelogram p=(Parallelogram)q; //向下转型操作
  
}
  
if(q instanceof Square){ //判断父类对象是否为Parallelogram子类的一个实例
  
Square s=(Square)q; //进行向下转型操作
  
}
  
//由于q对象不为Anything类的对象，所以这条语句是错误的
  
//System.out.println(q instanceof Anything);
  
}
  
}
  
在本实例中将instanceof操作符与向下转型操作结合使用。在程序中定义了两个子类，即平行四边形类和正方形类，这两个类分别继承四边形类。在主方法中首先创建四边形类对象，然后使用instanceof操作符判断四边形类对象是否为平行四边形类的一个实例，是否为正方形类的一个实例，如果判断结果为true，将进行向下转型操作。

http://book.51cto.com/art/200810/92220.htm