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
	"github.com/gocolly/colly/queue"
	"github.com/gocolly/redisstorage"
)

func main() {
	urls := []string{
		"http://httpbin.org/",
		"http://httpbin.org/ip",
		"http://httpbin.org/cookies/set?a=b&c=d",
		"http://httpbin.org/cookies",
	}

	c := colly.NewCollector()

	// create the redis storage
	storage := &redisstorage.Storage{
		Address:  "127.0.0.1:6379",
		Password: "",
		DB:       0,
		Prefix:   "httpbin_test",
	}

	// add storage to the collector
	err := c.SetStorage(storage)
	if err != nil {
		panic(err)
	}

	// delete previous data from storage
	if err := storage.Clear(); err != nil {
		log.Fatal(err)
	}

	// close redis client
	defer storage.Client.Close()

	// create a new request queue with redis storage backend
	q, _ := queue.New(2, storage)

	c.OnResponse(func(r *colly.Response) {
		log.Println("Cookies:", c.Cookies(r.Request.URL.String()))
	})

	// add URLs to the queue
	for _, u := range urls {
		q.AddURL(u)
	}
	// consume requests
	q.Run(c)
}
```
