---
title: Crawler configuration
categories: [crawling]
menu:
  docs:
    parent: "best_practices"
    weight: 40
---

Colly's default configuration is optimized for scraping smaller number of sites in one job. This setup isn't the best if you'd like to crawl millions of sites. Here are some tweaks:


## Use persistent storage backend

By default Colly stores cookies and visited URLs in memory. You can replace the built-in in-memory storage backend with any custom backend. See more details [here](https://godoc.org/github.com/gocolly/colly/storage).


## Use async for long running jobs with recursive calls

By default Colly blocks while the request isn't finished, so recursively calling `Collector.Visit` from callbacks produces constantly growing stack. With `Collector.Async = true` this can be avoided.
(Don't forget to use `c.Wait()` with async.)

## Disable or limit connection keep-alive

Colly uses HTTP keep-alive to enhance scraping speed. It requires open file descriptors, so max-fd limit can be easily reached with long running jobs.

HTTP Keep-alive can be disabled with the following code:

```go
c := colly.NewCollector()
c.WithTransport(&http.Transport{
    DisableKeepAlives: true,
})
```
