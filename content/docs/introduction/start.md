---
title: Getting started
categories: [start]
menu:
  docs:
    parent: "introduction"
    weight: 30
---

Before working with Colly ensure that you have the latest version. See [installation guide](/docs/introduction/install/) for more details.


Let's get started with some simple examples.


First, you need to import Colly to your codebase:

```go
import "github.com/gocolly/colly"
```


## Collector

Colly's main entity is a `Collector` object. `Collector` manages the network communication and responsible for the execution of the attached callbacks while a collector job is running. To work with colly, you have to initialize a `Collector`:

```go
c := colly.NewCollector()
```


## Callbacks


You can attach different type of callback functions to a `Collector` to control a collecting job or retrieve information. Check out the [related section](https://godoc.org/github.com/gocolly/colly#Collector.OnError) in the package documentation.


### Add callbacks to a `Collector`


```go
c.OnRequest(func(r *colly.Request) {
    fmt.Println("Visiting", r.URL)
})

c.OnError(func(_ *colly.Response, err error) {
    log.Println("Something went wrong:", err)
})

c.OnResponse(func(r *colly.Response) {
    fmt.Println("Visited", r.URL)
})

c.OnHTML("a[href]", func(e *colly.HTMLElement) {
    e.Request.Visit(e.Attr("href"))
})

c.OnHTML("tr td:nth-of-type(1)", func(e *colly.HTMLElement) {
    fmt.Println("First column of a table row:", e.Text)
})

c.OnScraped(func(r *colly.Response) {
    fmt.Println("Finished", r.URL)
})
```


### Call order of callbacks

#### 1. `OnRequest`

Called before a request

#### 2. `OnError`

Called if error occured during the request

#### 3. `OnResponse`

Called after response received

#### 4. `OnHTML`

Called right after `OnResponse` if the received content is HTML

#### 5. `OnScraped`

Called after `OnHTML` callbacks
