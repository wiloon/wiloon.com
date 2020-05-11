---
title: golang json
author: wiloon
type: post
date: 2019-01-21T07:05:05+00:00
url: /?p=13442
categories:
  - Uncategorized

---
https://colobu.com/2017/06/21/json-tricks-in-Go/

<pre><code class="language-go line-numbers">type Result struct {
    Count int `json:"count"`
}

func main() {
    out := shellExec(shell)
    var result Result
    json.Unmarshal([]byte(out), &result)
    fmt.Println(result.Count)
}

</code></pre>