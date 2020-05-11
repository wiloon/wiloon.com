---
title: raspberry pi gpio
author: wiloon
type: post
date: 2019-07-09T16:04:46+00:00
url: /?p=14659
categories:
  - Uncategorized

---
继电器: 树莓派的GPIO口是3.3V的，你需要把它转化成5V才能进行控制

<blockquote class="wp-embedded-content" data-secret="57KOUtczxY">
  <p>
    <a href="http://blog.oa25.org/?p=472">Raspberry Pi 的GPIO接口电路</a>
  </p>
</blockquote>

<iframe title="《Raspberry Pi 的GPIO接口电路》—oa25开源日记" class="wp-embedded-content" sandbox="allow-scripts" security="restricted" style="position: absolute; clip: rect(1px, 1px, 1px, 1px);" src="http://blog.oa25.org/?p=472&#038;embed=true#?secret=57KOUtczxY" data-secret="57KOUtczxY" width="600" height="338" frameborder="0" marginwidth="0" marginheight="0" scrolling="no"></iframe>

https://www.kidscoding8.com/47249.html

<pre><code class="language-bash line-numbers">#使GPIO17从内核空间暴露到用户空间中
sudo echo 17 &gt; /sys/class/gpio/export
#设置GPIO17为输出模式
sudo echo out &gt; /sys/class/gpio/gpio17/direction
#向value文件中输入1，GPIO输出高电平，LED点亮
sudo echo 1 &gt; /sys/class/gpio/gpio17/value
#向value文件中输入0，GPIO输出低电平，LED熄灭
sudo echo 0 &gt; /sys/class/gpio/gpio17/value
#注销GPIO17接口
sudo echo 17 &gt; /sys/class/gpio/unexport
</code></pre>

<pre><code class="language-bash line-numbers">sudo vim ledonoff.sh
echo $1 &gt; /sys/class/gpio/export
echo out &gt; /sys/class/gpio/gpio$1/direction
echo 1 &gt; /sys/class/gpio/gpio$1/value
sleep 5 #延时5秒
echo 0 &gt; /sys/class/gpio/gpio$1/value
echo $1 &gt; /sys/class/gpio/unexport

sudo ./ledonoff.sh 17
</code></pre>

https://zhuanlan.zhihu.com/p/40594358
  
https://www.huanxiangke.com/index.php/blog/post/voice-control-to-transform-ordinary-desk-lamp