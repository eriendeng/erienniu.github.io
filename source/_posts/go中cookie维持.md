---
title: go中cookie维持
date: 2018-07-25 14:52:41
tags:
---

使用cookieJa对response中的cookie进行存储

```go

import "net/http/cookiejar"

func main() {
    var client http.Client
    jar, err := cookiejar.New(nil)
    if err != nil {
        panic(err)
    }
    client.Jar = jar

    client.Post(...) // 在这里登陆
    client.Get()     // 后续请求client会自动将cookie加入
}

```