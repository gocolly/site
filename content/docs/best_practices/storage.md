---
title: Storage backend
categories: [storage]
menu:
  docs:
    parent: "best_practices"
    weight: 25
---

Colly has an in-memory storage backend to store cookies and visited URLs, but it can be overwritten by any custom storage backend which implements [colly/storage.Storage](https://godoc.org/github.com/gocolly/colly/storage#Storage).

## Existing Backends

### In-Memory Backend

The default backend of Colly. Use [collector.SetStorage()](https://godoc.org/github.com/gocolly/colly#Collector.SetStorage) to override.


### [Redis backend](https://github.com/gocolly/redisstorage)
