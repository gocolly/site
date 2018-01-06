---
title: Crawler configuration
categories: [crawling]
menu:
  docs:
    parent: "best_practices"
    weight: 40
---

Colly's default configuration is optimized for scraping smaller number of sites in one job. This setup isn't the best if you'd like to crawl millions of sites. Here are some tweaks:

```go
// Reduce maximum response body size to 1M
// Don't track visited urls automatically
c := c.NewCollector(colly.MaxBodySize(1024 * 1024), colly.AllowURLRevisit())

// Turn off cookie handling
c.DisableCookies()
```

If you need cookies anyway, a persistent cookie storage can be used (e.g. https://github.com/juju/persistent-cookiejar) :

```go
// Don't forget to import the cookie jar
import "https://github.com/juju/persistent-cookiejar"

j, err := cookiejar.New(&cookiejar.Options{Filename: "cookie.db"})
if err == nil {
    c.SetCookieJar(j)
}
```
