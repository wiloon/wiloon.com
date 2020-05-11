---
title: go channel
author: wiloon
type: post
date: 2017-11-09T06:11:50+00:00
url: /?p=11392
categories:
  - Uncategorized

---
http://colobu.com/2016/04/14/Golang-Channels/

Channel是Go中的一个核心类型，你可以把它看成一个管道，通过它并发核心单元就可以发送或者接收数据进行通讯(communication)。

<pre><code class="language-go line-numbers">&lt;-          // 它的操作符是箭头
ch &lt;- v     // 发送值v到Channel ch中
v := &lt;-ch   // 从Channel ch中接收数据，并将数据赋值给v
// (箭头的指向就是数据的流向)

// channel 定义
var dataChan &lt;-chan []byte

// channel 初始化
dataChan = make(&lt;-chan []byte)
</code></pre>

就像 map 和 slice 数据类型一样, channel必须先创建再使用

<pre><code class="language-go line-numbers">// 创建 channel
ch := make(chan int)

// 使用make初始化Channel,并且可以设置容量:
make(chan int, 100)
</code></pre>

### for 可以处理channel

<pre><code class="language-go line-numbers">for i := range c {
        fmt.Println(i)
    }
</code></pre>

receive 操作符
  
<-ch用来从channel ch中接收数据，这个表达式会一直被block,直到有数据可以接收。
  
从一个nil channel中接收数据会一直被block。

从一个被close的channel中接收数据不会被阻塞，而是立即返回，接收完已发送的数据后会返回元素类型的零值(zero value)。

如前所述，你可以使用一个额外的返回参数来检查channel是否关闭。