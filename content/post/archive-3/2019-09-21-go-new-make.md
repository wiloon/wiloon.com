---
title: go new make
author: wiloon
type: post
date: 2019-09-21T09:41:51+00:00
url: /?p=14943
categories:
  - Uncategorized

---
https://sanyuesha.com/2017/07/26/go-make-and-new/

new 和 make 都可以用来分配空间，初始化类型，但是它们确有不同。

new(T) 返回的是 T 的指针
  
new(T) 为一个 T 类型新值分配空间并将此空间初始化为 T 的零值，返回的是新值的地址，也就是 T 类型的指针 *T，该指针指向 T 的新分配的零值。

p1 := new(int)
  
fmt.Printf(&#8220;p1 &#8211;> %#v \n &#8220;, p1) //(*int)(0xc42000e250)
  
fmt.Printf(&#8220;p1 point to &#8211;> %#v \n &#8220;, *p1) //0

var p2 _int
  
i := 0
  
p2 = &i
  
fmt.Printf(&#8220;p2 &#8211;> %#v \n &#8220;, p2) //(_int)(0xc42000e278)
  
fmt.Printf(&#8220;p2 point to &#8211;> %#v \n &#8220;, *p2) //0
  
上面的代码是等价的，new(int) 将分配的空间初始化为 int 的零值，也就是 0，并返回 int 的指针，这和直接声明指针并初始化的效果是相同的。

make 只能用于 slice,map,channel
  
make 只能用于 slice，map，channel 三种类型，make(T, args) 返回的是初始化之后的 T 类型的值，这个新值并不是 T 类型的零值，也不是指针 *T，是经过初始化之后的 T 的引用。

var s1 []int
  
if s1 == nil {
      
fmt.Printf(&#8220;s1 is nil &#8211;> %#v \n &#8220;, s1) // []int(nil)
  
}

s2 := make([]int, 3)
  
if s2 == nil {
      
fmt.Printf(&#8220;s2 is nil &#8211;> %#v \n &#8220;, s2)
  
} else {
      
fmt.Printf(&#8220;s2 is not nill &#8211;> %#v \n &#8220;, s2)// []int{0, 0, 0}
  
}
  
slice 的零值是 nil，使用 make 之后 slice 是一个初始化的 slice，即 slice 的长度、容量、底层指向的 array 都被 make 完成初始化，此时 slice 内容被类型 int 的零值填充，形式是 [0 0 0]，map 和 channel 也是类似的。

var m1 map[int]string
  
if m1 == nil {
      
fmt.Printf(&#8220;m1 is nil &#8211;> %#v \n &#8220;, m1) //map[int]string(nil)
  
}

m2 := make(map[int]string)
  
if m2 == nil {
      
fmt.Printf(&#8220;m2 is nil &#8211;> %#v \n &#8220;, m2)
  
} else {
      
fmt.Printf(&#8220;m2 is not nill &#8211;> %#v \n &#8220;, m2) map[int]string{}
  
}

var c1 chan string
  
if c1 == nil {
      
fmt.Printf(&#8220;c1 is nil &#8211;> %#v \n &#8220;, c1) //(chan string)(nil)
  
}

c2 := make(chan string)
  
if c2 == nil {
      
fmt.Printf(&#8220;c2 is nil &#8211;> %#v \n &#8220;, c2)
  
} else {
      
fmt.Printf(&#8220;c2 is not nill &#8211;> %#v \n &#8220;, c2)//(chan string)(0xc420016120)
  
}
  
make(T, args) 返回的是 T 的 引用
  
如果不特殊声明，go 的函数默认都是按值穿参，即通过函数传递的参数是值的副本，在函数内部对值修改不影响值的本身，但是 make(T, args) 返回的值通过函数传递参数之后可以直接修改，即 map，slice，channel 通过函数穿参之后在函数内部修改将影响函数外部的值。

func modifySlice(s []int) {
      
s[0] = 1
  
}

s2 := make([]int, 3)
  
fmt.Printf(&#8220;%#v&#8221;, s2) //[]int{0, 0, 0}
  
modifySlice(s2)
  
fmt.Printf(&#8220;%#v&#8221;, s2) //[]int{1, 0, 0}
  
这说明 make(T, args) 返回的是引用类型，在函数内部可以直接更改原始值，对 map 和 channel 也是如此。

func modifyMap(m map[int]string) {
      
m[0] = &#8220;string&#8221;
  
}

func modifyChan(c chan string) {
      
c <- &#8220;string&#8221;
  
}

m2 := make(map[int]string)
  
if m2 == nil {
      
fmt.Printf(&#8220;m2 is nil &#8211;> %#v \n &#8220;, m2)
  
} else {
      
fmt.Printf(&#8220;m2 is not nill &#8211;> %#v \n &#8220;, m2) //map[int]string{}
  
}

modifyMap(m2)
  
fmt.Printf(&#8220;m2 is not nill &#8211;> %#v \n &#8220;, m2) // map[int]string{0:&#8221;string&#8221;}

c2 := make(chan string)
  
if c2 == nil {
      
fmt.Printf(&#8220;c2 is nil &#8211;> %#v \n &#8220;, c2)
  
} else {
      
fmt.Printf(&#8220;c2 is not nill &#8211;> %#v \n &#8220;, c2)
  
}

go modifyChan(c2)
  
fmt.Printf(&#8220;c2 is not nill &#8211;> %#v &#8220;, <-c2) //&#8221;string&#8221;
  
很少需要使用 new
  
以下代码演示了 struct 初始化的过程，可以说明不使用 new 一样可以完成 struct 的初始化工作。

type Foo struct {
      
name string
      
age int
  
}

//声明初始化
  
var foo1 Foo
  
fmt.Printf(&#8220;foo1 &#8211;> %#v\n &#8220;, foo1) //main.Foo{age:0, name:&#8221;&#8221;}
  
foo1.age = 1
  
fmt.Println(foo1.age)

//struct literal 初始化
  
foo2 := Foo{}
  
fmt.Printf(&#8220;foo2 &#8211;> %#v\n &#8220;, foo2) //main.Foo{age:0, name:&#8221;&#8221;}
  
foo2.age = 2
  
fmt.Println(foo2.age)

//指针初始化
  
foo3 := &Foo{}
  
fmt.Printf(&#8220;foo3 &#8211;> %#v\n &#8220;, foo3) //&main.Foo{age:0, name:&#8221;&#8221;}
  
foo3.age = 3
  
fmt.Println(foo3.age)

//new 初始化
  
foo4 := new(Foo)
  
fmt.Printf(&#8220;foo4 &#8211;> %#v\n &#8220;, foo4) //&main.Foo{age:0, name:&#8221;&#8221;}
  
foo4.age = 4
  
fmt.Println(foo4.age)

//声明指针并用 new 初始化
  
var foo5 _Foo = new(Foo)
  
fmt.Printf(&#8220;foo5 &#8211;> %#v\n &#8220;, foo5) //&main.Foo{age:0, name:&#8221;&#8221;}
  
foo5.age = 5
  
fmt.Println(foo5.age)
  
foo1 和 foo2 是同样的类型，都是 Foo 类型的值，foo1 是通过 var 声明，Foo 的 filed 自动初始化为每个类型的零值，foo2 是通过字面量的完成初始化。foo3，foo4 和 foo5 是一样的类型，都是 Foo 的指针 Foo。_但是所有 foo 都可以直接使用 Foo 的 filed，读取或修改，为什么？

如果 x 是可寻址的，&x 的 filed 集合包含 m，x.m 和 (&x).m 是等同的，go 自动做转换，也就是 foo1.age 和 foo3.age 调用是等价的，go 在下面自动做了转换。

因而可以直接使用 struct literal 的方式创建对象，能达到和 new 创建是一样的情况而不需要使用 new。

小结
  
new(T) 返回 T 的指针 *T 并指向 T 的零值。
  
make(T) 返回的初始化的 T，只能用于 slice，map，channel。