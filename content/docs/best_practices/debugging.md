---
title: Debugging
categories: [debugging]
menu:
  docs:
    parent: "best_practices"
    weight: 10
---

Sometimes it's enough to place some `log.Println()` function calls to your callbacks, but sometimes it isn't. Colly has built-in abilities for collector debug. A debugger interface and different kind of debugger implementations are available.

## Attach debugger to a collector

Attaching a basic logging debugger requires the `debug` (`github.com/gocolly/colly/v2/debug`) package from Colly's repo.

```go

import (
	"github.com/gocolly/colly/v2"
	"github.com/gocolly/colly/v2/debug"
)

func main() {
    c := colly.NewCollector(colly.Debugger(&debug.LogDebugger{}))
    // [..]
}
```

## Implement a custom debugger

You can create any kind of custom debugger by implementing the [debug.Debugger](https://godoc.org/github.com/gocolly/colly/v2/debug#Debugger) interface. A good example is [LogDebugger](https://github.com/gocolly/colly/blob/master/debug/logdebugger.go).
