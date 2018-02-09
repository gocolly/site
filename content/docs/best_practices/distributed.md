---
title: Distributed Scraping
categories: [distributed]
menu:
  docs:
    parent: "best_practices"
    weight: 15
---

Distributed scraping can be implemented in different ways depending on what the requirements of the scraping task are. Most of the time it's enough to scale the network communication layer which can be easily achieved using proxies and Colly's proxy switchers.


## Proxy switchers

Using proxy switchers scraping still remains centralized while the HTTP requests are distributed among multiple proxies. Colly supports proxy switching via its' `SetProxyFunc()` member. Any custom function can be passed to `SetProxyFunc()` with the signature of `func(*http.Request) (*url.URL, error)`.

{{<tip>}}SSH servers can be used as socks5 proxies with the <code>-D</code> flag.{{</tip>}}

Colly has a built-in proxy switcher which rotates a list of proxies on every request.

### Usage

```go
package main

import (
	"github.com/gocolly/colly"
	"github.com/gocolly/colly/proxy"
)

func main() {
	c := colly.NewCollector()

	if p, err := proxy.RoundRobinProxySwitcher(
		"socks5://127.0.0.1:1337",
		"socks5://127.0.0.1:1338",
		"http://127.0.0.1:8080",
	); err == nil {
		c.SetProxyFunc(p)
	}
	// ...
}
```

Implementing custom proxy switcher:

```go
var proxies []*url.URL = []*url.URL{
	&url.URL{Host: "127.0.0.1:8080"},
	&url.URL{Host: "127.0.0.1:8081"},
}

func randomProxySwitcher(_ *http.Request) (*url.URL, error) {
	return proxies[random.Intn(len(proxies))], nil
}

// ...
c.SetProxyFunc(randomProxySwitcher)
```


## Distributed scrapers

To manage independent and distributed scrapers the best you can do is wrapping the scraper in a server. Server can be any kind of service like HTTP, TCP servers or Google App Engine. Use custom [docs/best_practices/storage](storage) to achieve centralized and persistent cookie and visited url handling.

{{<tip>}}Colly has built-in Google App Engine support. Don't forget to call <code>Collector.Appengine(*http.Request)</code> if you use Colly from App Engine standard environment.{{</tip>}}

### Distributed storage

Visited URL and cookie data are stored in-memory by default. This is handy for short living scraper jobs, but it can be a serious limitation when dealing with large scale or long running crawling jobs.

Colly has the ability to replace the default in-memory storage with any storage backend which implements [colly/storage.Storage](https://godoc.org/github.com/gocolly/colly/storage#Storage) interface. Check out [docs/best_practices/storage](existing storages).
