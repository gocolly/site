---
title: Storage backend
categories: [storage]
menu:
  docs:
    parent: "best_practices"
    weight: 25
---

Colly has an in-memory storage backend to store cookies and visited URLs, but it can be overwritten by any custom storage backend which implements [colly/v2/storage.Storage](https://godoc.org/github.com/gocolly/colly/v2/storage#Storage).

## Existing Storage Backends

### [In-Memory Backend](https://godoc.org/github.com/gocolly/colly/v2/storage#InMemoryStorage)

The default backend of Colly. Use [collector.SetStorage()](https://godoc.org/github.com/gocolly/colly/v2#Collector.SetStorage) to override.

### [Redis backend](https://github.com/gocolly/redisstorage)

See [redis example](/docs/examples/redis_backend) for details.

### [boltdb backend](https://github.com/earlzo/colly-bolt-storage)

### [SQLite3 backend](https://github.com/velebak/colly-sqlite3-storage)

### [MongoDB backend](https://github.com/zolamk/colly-mongo-storage)

### [PostgreSQL backend](https://github.com/zolamk/colly-postgres-storage)
