---
title: Extensions
categories: [debugging]
menu:
  docs:
    parent: "best_practices"
    weight: 50
---

Extensions are small helper utilities shipped with Colly. List of plugins is available [here](https://godoc.org/github.com/gocolly/colly/v2/extensions).


## Usage

The following example enables the random User-Agent switcher and the Referrer setter extension and visits httpbin.org twice.

```go
import (
    "log"

    "github.com/gocolly/colly/v2"
    "github.com/gocolly/colly/v2/extensions"
)

func main() {
    c := colly.NewCollector()
    visited := false

    extensions.RandomUserAgent(c)
    extensions.Referer(c)

    c.OnResponse(func(r *colly.Response) {
        log.Println(string(r.Body))
        if !visited {
            visited = true
            r.Request.Visit("/get?q=2")
        }
    })

    c.Visit("http://httpbin.org/get")
}

```
