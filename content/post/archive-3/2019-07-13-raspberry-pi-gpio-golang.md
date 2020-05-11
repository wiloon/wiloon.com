---
title: raspberry pi, gpio, golang
author: wiloon
type: post
date: 2019-07-13T08:00:44+00:00
url: /?p=14679
categories:
  - Uncategorized

---
https://github.com/stianeikeland/go-rpio

<pre><code class="language-go line-numbers">package main

import (
    "fmt"
    "github.com/stianeikeland/go-rpio/v4"
    "time"
)

func main() {

    err := rpio.Open()
    if err != nil {
        fmt.Println(err)
        return
    }
    pin := rpio.Pin(17)

    pin.Low()
    time.Sleep(5 * time.Second)
    pin.High()

}

</code></pre>