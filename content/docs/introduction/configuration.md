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

Full list of collector attributes can be found [here](https://godoc.org/github.com/gocolly/colly/v2#Collector).
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
or
```go
c2 := colly.NewCollector()
c2.UserAgent = "xy"
c2.AllowURLRevisit = true
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
	r.Headers.Set("User-Agent", RandomString())
})
```

## Configuration via environment variables

Collector's default configuration can be changed via environment variables. This allows us to fine-tune collectors without recompile.
Environment parsing is the last step of the collector initialization, so every configuration change after the initialization overrides the config parsed from environment.

### Environment config variables


 - **`COLLY_ALLOWED_DOMAINS`** (comma separated list of domains)
 - **`COLLY_CACHE_DIR`** (string)
 - **`COLLY_DETECT_CHARSET`** (y/n)
 - **`COLLY_DISABLE_COOKIES`** (y/n)
 - **`COLLY_DISALLOWED_DOMAINS`** (comma separated list of domains)
 - **`COLLY_IGNORE_ROBOTSTXT`** (y/n)
 - **`COLLY_FOLLOW_REDIRECTS`** (y/n)
 - **`COLLY_MAX_BODY_SIZE`** (int)
 - **`COLLY_MAX_DEPTH`** (int - 0 means infinite)
 - **`COLLY_PARSE_HTTP_ERROR_RESPONSE`** (y/n)
 - **`COLLY_USER_AGENT`** (string)


## HTTP configuration

Colly uses Golang's default [http client](https://godoc.org/net/http) as networking layer. HTTP options can be tweaked by changing the default [HTTP roundtripper](https://godoc.org/net/http#RoundTripper).

```go
c := colly.NewCollector()
c.WithTransport(&http.Transport{
	Proxy: http.ProxyFromEnvironment,
	DialContext: (&net.Dialer{
		Timeout:   30 * time.Second,
		KeepAlive: 30 * time.Second,
		DualStack: true,
	}).DialContext,
	MaxIdleConns:          100,
	IdleConnTimeout:       90 * time.Second,
	TLSHandshakeTimeout:   10 * time.Second,
	ExpectContinueTimeout: 1 * time.Second,
})
```
