---
title: Configuration
categories: [introduction]
menu:
  docs:
    parent: "introduction"
    weight: 50
---

Colly is a highly customizable scraping framework. It has sane defaults and provides plenty of options to change them.


## Collector configuration

Full list of collector attributes can be found [here](https://godoc.org/github.com/gocolly/colly#Collector).
The recommended way to initialize a collector is using `colly.NewCollector(options...)`.

Create a collector with default settings:
```go
c1 := colly.NewCollector()
```

Create another collector and change User-Agent and url revisit options:
```go
c2 := colly.NewCollector(
	colly.UserAgent("xy"),
	colly.AllowURLRevisit(),
)
```

Configuration can be changed at any point of a scraping job by overwriting the attributes of the collectors.

A good example is a User-Agent switcher which changes User-Agent on every request:
```go
const letterBytes = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"

func RandomString() string {
	b := make([]byte, rand.Intn(10)+10)
	for i := range b {
		b[i] = letterBytes[rand.Intn(len(letterBytes))]
	}
	return string(b)
}

c := colly.NewCollector()

c.OnRequest(func(r *colly.Request) {
	r.UserAgent = RandomString()
})
```

## HTTP configuration

Colly uses Golang's default [http client](https://godoc.org/net/http) as the networking layer. HTTP options can be tweaked by changing the default [HTTP roundtripper](https://godoc.org/net/http#RoundTripper).

```go
c := colly.NewCollector()
c.WithTransport(&http.Transport{
	Proxy: ProxyFromEnvironment,
	DialContext: (&net.Dialer{
		Timeout:   30 * time.Second,
		KeepAlive: 30 * time.Second,
		DualStack: true,
	}).DialContext,
	MaxIdleConns:          100,
	IdleConnTimeout:       90 * time.Second,
	TLSHandshakeTimeout:   10 * time.Second,
	ExpectContinueTimeout: 1 * time.Second,
}
```
