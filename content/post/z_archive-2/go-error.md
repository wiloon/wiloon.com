---
title: golang 异常处理, err, error, panic, recover
author: "-"
date: 2016-07-01T08:25:40+00:00
url: go/error
categories:
  - Go
tags:
  - reprint
---
## golang 异常处理, err, error, panic, recover

### 基础知识

错误指的是可能出现问题的地方出现了问题, 比如打开一个文件时失败, 这种情况在人们的意料之中；而异常指的是不应该出现问题的地方出现了问题, 比如引用了空指针, 这种情况在人们的意料之外。可见, 错误是业务过程的一部分, 而异常不是。

### 自定义异常

```go
return errors.New("string")
```

### 打印调用栈

#### using runtime

```go
defer func() {
    if p := recover(); p != nil {
        logger.Errorf("panic: %v", p)
        //打印调用栈信息
        buf := make([]byte, 2048)
        n := runtime.Stack(buf, false)
        stackInfo := fmt.Sprintf("%s", buf[:n])
        logger.Errorf("panic stack info %s", stackInfo)
    }
}()

```

#### using debug

```go
if p := recover(); p != nil {
    logs.Error("message push panic: %v", p)

    //打印调用栈信息
    debug.PrintStack()
}
```

Go语言追求简洁优雅, 所以,Go语言不支持传统的 try…catch…finally 这种异常,因为Go语言的设计者们认为,将异常与控制结构混在一起会很容易使得代码变得混乱。因为开发者很容易滥用异常,甚至一个小小的错误都抛出一个异常。在Go语言中,使用多值返回来返回错误。不要用异常代替错误,更不要用来控制流程。在极个别的情况下,也就是说,遇到真正的异常的情况下 (比如除数为0了) 。才使用Go中引入的Exception处理: defer, panic, recover。

这几个异常的使用场景可以这么简单描述: Go中可以抛出一个panic 的异常,然后在defer 中通过recover 捕获这个异常,然后正常处理。

### defer

```go
func main(){
    defer func() {  // 必须要先声明defer,否则不能捕获到panic异常
    fmt.Println("c")
    if err:=recover();err!=nil {
        fmt.Println(err) // 这里的 err 其实就是panic传入的内容,55
    }
    fmt.Println("d")
    }()
f()
}

func f(){
    fmt.Println("a")
    panic(55)
    fmt.Println("b")
    fmt.Println("f")
}
```

#### 输出结果

```bash
a
c
d
```
  
exit code 0, process exited normally.

参考:  [http://blog.csdn.net/ghost911_slb/article/details/7831574](http://blog.csdn.net/ghost911_slb/article/details/7831574)

defer 英文原意:  vi. 推迟；延期；服从   vt. 使推迟；使延期。  

defer的思想类似于C++中的析构函数,不过Go语言中"析构"的不是对象,而是函数,defer就是用来添加函数结束时执行的语句。注意这里强调的是添加,而不是指定,因为不同于C++中的析构函数是静态的,Go中的defer是动态的。

```bash
func f() (result int) {

defer func() {

result++

}()

return 0

}
```
  
上面函数返回1,因为defer中添加了一个函数,在函数返回前改变了命名返回值的值。是不是很好用呢。但是,要注意的是,如果我们的defer语句没有执行,那么defer的函数就不会添加,如果把上面的程序改成这样:

func f() (result int) {

return 0
  
defer func() {
  
result++
  
}()
  
return 0
  
}
  
上面的函数就返回0了,因为还没来得及添加defer的东西,函数就返回了。

另外值得一提的是,defer可以多次,这样形成一个defer栈,后defer的语句在函数返回时将先被调用。

参考:  [http://weager.sinaapp.com/?p=31](http://weager.sinaapp.com/?p=31)

### panic

panic 英文原意: n. 恐慌,惊慌；大恐慌  adj. 恐慌的；没有理由的  vt. 使恐慌  vi. 十分惊慌

panic 是用来表示非常严重的不可恢复的错误的。在Go 语言中这是一个内置函数, 接收一个 interface{}类型的值 (也就是任何值了) 作为参数。panic 的作用就像我们平常接触的异常。不过Go 可没有try…catch,所以,panic 一般会导致程序挂掉 (除非recover) 。所以, Go语言中的异常, 那真的是异常了。你可以试试,调用panic 看看, 程序立马挂掉, 然后Go运行时会打印出调用栈。
  
但是,关键的一点是,即使函数执行的时候panic 了, 函数不往下走了, 运行时并不是立刻向上传递panic, 而是到defer那,等defer 的东西都跑完了,panic 再向上传递。所以这时候 defer 有点类似 try-catch-finally 中的 finally。
  
panic 就是这么简单。抛出个真正意义上的异常。

### recover

recover 英文原意:  vt. 恢复；弥补；重新获得   vi. 恢复；胜诉；重新得球   n. 还原至预备姿势

上面说到,panic 的函数并不会立刻返回,而是先defer,再返回。这时候 (defer的时候) ,如果有办法将panic捕获到,并阻止panic传递,那就异常的处理机制就完善了。

Go语言提供了recover内置函数,前面提到,一旦panic,逻辑就会走到defer那,那我们就在defer那等着,调用recover函数将会捕获到当前的panic (如果有的话) ,被捕获到的panic就不会向上传递了,于是,世界恢复了和平。你可以干你想干的事情了。

不过要注意的是,recover之后,逻辑并不会恢复到panic那个点去,函数还是会在defer之后返回。
  
用Go实现类似 try catch 的异常处理有个例子在:
  
[http://www.douban.com/note/238705941/](http://www.douban.com/note/238705941/)
  
结论:
  
Go对待异常 (准确的说是panic) 的态度就是这样,没有全面否定异常的存在,同时极力不鼓励多用异常。
  
参考: [http://blog.dccmx.com/2012/01/exception-the-go-way/](http://blog.dccmx.com/2012/01/exception-the-go-way/)
  
[http://kejibo.com/golang-exceptions-handle-defer-try/](http://kejibo.com/golang-exceptions-handle-defer-try/)
  
[http://bookjovi.iteye.com/blog/1335282](http://bookjovi.iteye.com/blog/1335282)
  
[https://github.com/astaxie/build-web-application-with-golang/blob/master/02.3.md](https://github.com/astaxie/build-web-application-with-golang/blob/master/02.3.md)
  
[http://www.cnblogs.com/ghj1976/archive/2013/02/11/2910114.html](http://www.cnblogs.com/ghj1976/archive/2013/02/11/2910114.html)
  
[https://blog.csdn.net/whynottrythis/article/details/78824471](https://blog.csdn.net/whynottrythis/article/details/78824471)

[http://xiaorui.cc/2016/03/15/%e4%bd%bf%e7%94%a8golang-errors%e5%8c%85%e6%9d%a5%e8%87%aa%e5%ae%9a%e4%b9%89%e9%94%99%e8%af%af%e5%bc%82%e5%b8%b8/](http://xiaorui.cc/2016/03/15/%e4%bd%bf%e7%94%a8golang-errors%e5%8c%85%e6%9d%a5%e8%87%aa%e5%ae%9a%e4%b9%89%e9%94%99%e8%af%af%e5%bc%82%e5%b8%b8/)

[http://xiaorui.cc/2016/03/15/%e4%bd%bf%e7%94%a8golang-errors%e5%8c%85%e6%9d%a5%e8%87%aa%e5%ae%9a%e4%b9%89%e9%94%99%e8%af%af%e5%bc%82%e5%b8%b8/embed/#?secret=W5Zgh6Plr7](http://xiaorui.cc/2016/03/15/%e4%bd%bf%e7%94%a8golang-errors%e5%8c%85%e6%9d%a5%e8%87%aa%e5%ae%9a%e4%b9%89%e9%94%99%e8%af%af%e5%bc%82%e5%b8%b8/embed/#?secret=W5Zgh6Plr7)

作者: _张晓龙_
链接: [https://www.jianshu.com/p/f30da01eea97](https://www.jianshu.com/p/f30da01eea97)
来源: 简书
著作权归作者所有。商业转载请联系作者获得授权,非商业转载请注明出处。
