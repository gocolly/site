---
title: "redis backend"
categories: [examples]
menu:
  docs:
    parent: "examples"
---

```go
package main

import (
	"log"

	"github.com/gocolly/colly"
	"github.com/gocolly/redisstorage"
)

func main() {
	urls := []string{
		"http://httpbin.org/",
		"http://httpbin.org/cookies/set?a=b&c=d",
		"http://httpbin.org/cookies",
	}

	c := colly.NewCollector()

	storage := &redisstorage.Storage{
		Address:  "127.0.0.1:6379",
		Password: "",
		DB:       0,
		Prefix:   "job01",
	}

	defer storage.Close()

	err := c.SetStorage(storage)
	if err != nil {
		panic(err)
	}

	c.OnResponse(func(r *colly.Response) {
		log.Println("Cookies:", c.Cookies(r.Request.URL.String()))
	})

	for _, u := range urls {
		if err := c.Visit(u); err != nil {
			log.Fatal(err)
		}
	}
}
```
