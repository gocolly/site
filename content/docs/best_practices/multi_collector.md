---
title: Using multiple collectors
categories: [multiple_collectors]
menu:
  docs:
    parent: "best_practices"
    weight: 30
---

It is advised to use multiple collectors for one scraping jobs if the task is complex enough or has different kind of subtasks. A good example is [coursera course scraper](/docs/examples/coursera_courses) where two collectors are used - one parses the list views and handles paging and the other one collects course details.

Colly has some built-in methods to support the usage of multiple collectors.

{{<tip>}}Use <code>collector.ID</code> in debugging to distinguish different collectors{{</tip>}}


## Cloning collectors

You can use the `Clone()` method of a collector if collectors have similar configuration. `Clone()` duplicates a collector with identical configuration but without the attached callbacks.

```go
c := colly.NewCollector(
	colly.UserAgent("myUserAgent"),
	colly.AllowedDomains("foo.com", "bar.com"),
)
// Custom User-Agent and allowed domains are cloned to c2
c2 := c.Clone()
```


## Passing custom data between collectors

Use collector's `Request()` function to be able to share context with other collectors.

Example of sharing context:

```go
c.OnResponse(func(r *colly.Response) {
	r.Ctx.Put(r.Headers.Get("Custom-Header"))
	c2.Request("GET", "https://foo.com/", nil, r.Ctx, nil)
}
```
