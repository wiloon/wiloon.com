---
title: golang WebAssembly
author: wiloon
type: post
date: 2019-05-10T10:19:33+00:00
url: /?p=14322
categories:
  - Uncategorized

---
https://github.com/golang/go/wiki/WebAssembly
  
https://tutorialedge.net/golang/go-webassembly-tutorial/

<pre><code class="language-go line-numbers">package main

import "fmt"

func main() {
    fmt.Println("Hello, WebAssembly!")
}
</code></pre>

<pre><code class="language-bash line-numbers">GOOS=js GOARCH=wasm go build -o main.wasm
cp "$(go env GOROOT)/misc/wasm/wasm_exec.js" .
go get -u github.com/shurcooL/goexec
goexec 'http.ListenAndServe(":8080", http.FileServer(http.Dir(".")))'


</code></pre>