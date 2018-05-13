---
title: Colly goes 1.0
description: Colly v1.0.0 release
date: 2018-05-13
author: kvch
---

We are happy to announce that the first major release of Colly is here. Our goal was to create a scraping framework to speed up development and let its users concentrate on collecting relevant data. There is no need to reinvent the wheel when writing a new collector. Scrapers built on top of Colly support different storage backends, dynamic configuration and running requests in parallel out of the box. It is also possible to run your scrapers in a distributed manner.

## Facts about the development

It started in September 2017 and has not stopped since. Colly has attracted numerous developers who helped by providing valuable feedback and contributing new features. Let's see the numbers. In the last seven months **30 contributors** have created **338 commits**. Users have opened **78 issues**. 74 of the those were resolved in a few days. Contributors have opened **59 pull requests** and all of them except for one are either got merged or closed. We would like to thank all of our supporters who either contributed code or wrote blog posts about Colly or helped development somehow. We would not be here without you.


## Milestones

<script src="https://cdnjs.cloudflare.com/ajax/libs/vis/4.21.0/vis.min.js"></script>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/vis/4.21.0/vis.min.css" type="text/css" media="screen" charset="utf-8">
<div id="timeline"></div>
<style>
.vis-timeline { border: 4px solid #224488; font-size: 9pt; background: #ececff; }
.vis-item { border-color: #3366AA; background-color: #FFF; font-size: 9pt; color: #224488; box-shadow: 5px 5px 20px rgba(128,128,128, 0.5); }
.vis-item, .vis-item.vis-line { border-width: 1px; }
.vis-item a { text-decoration: none; }
.vis-item.vis-dot { border-width: 10px; border-radius: 10px; }
.vis-item.vis-selected { border-color: green; background-color: lightgreen; }
.vis-time-axis .vis-text { color: #224488; padding-top: 10px; padding-left: 10px; }
.vis-time-axis .vis-text.vis-major { font-weight: bold; }
.vis-time-axis .vis-grid.vis-minor { border-width: 2px; border-color: pink; }
.vis-time-axis .vis-grid.vis-major { border-width: 2px; border-color: #F991A3; }
</style>
<script>
var container = document.getElementById('timeline');

// Create a DataSet (allows two way data-binding)
var items = new vis.DataSet([
{id: 1, content: '<a target="_blank" href="https://github.com/gocolly/colly/commit/bcc199e7c146756b0c076cbacd5ad357da4e2e2e">First commit</a>', start: '2017-09-29'},
{id: 2, content: '<a target="_blank" href="https://github.com/gocolly/colly/commit/7ddf8cbe6501b3fbae8be9f587da03b08ee4676a">First external contribution</a>', start: '2017-10-05'},
{id: 3, content: '<a target="_blank" href="https://github.com/gocolly/colly/commit/ba9dd651fb82b72779b35a1e2b20e4229c69b5f6">Caching implemented</a>', start: '2017-10-08'},
{id: 4, content: '<a target="_blank" href="https://github.com/gocolly/colly/commit/59257f38473e210580dfc669b357efff814a9431">Declarative HTML parsing</a>', start: '2017-11-10'},
{id: 5, content: '<a target="_blank" href="https://github.com/gocolly/colly">Repository moved to gocolly organization</a>', start: '2017-11-11'},
{id: 6, content: '<a target="_blank" href="https://github.com/gocolly/colly/commit/f454a6cb874e5b48414aca41c7b12018d14bf265">Debugger interface added</a>', start: '2017-11-14'},
{id: 7, content: '<a target="_blank" href="https://github.com/gocolly/colly/commit/877be4c4d6481e86fb04c16a694e06bca493ed73">XPath and XML support</a>', start: '2018-01-13'},
{id: 8, content: '<a target="_blank" href="https://github.com/gocolly/colly/commit/2103c5c4ba5bcc8017675876d74f0b6359b29125">Async crawling</a>', start: '2018-01-20'},
{id: 9, content: '<a target="_blank" href="https://github.com/gocolly/colly/commit/04d96ab7c5bbf879de632fcc683af592be4ba0b8">Storage interface implemented</a>', start: '2018-02-09'},
{id: 10, content: '<a target="_blank" href="https://github.com/gocolly/colly/commit/119df7afac3fbefd77c229bb366f9da77d5fc490">Performance optimizations (+25% speed)</a>', start: '2018-03-03'},
{id: 11, content: '<a target="_blank" href="https://github.com/gocolly/colly/tree/master/extensions">Extensions added</a>', start: '2018-03-12'},
{id: 12, content: '<a target="_blank" href="https://twitter.com/gocolly">Twitter account created</a>', start: '2018-03-13'},
{id: 13, content: '<a target="_blank" href="https://github.com/gocolly/colly/commit/6d5233174823d61db7151e7c644cee0bb50e4bf2">Requests queue interface</a>', start: '2018-04-13'},
{id: 14, content: '<a target="_blank" href="https://github.com/gocolly/colly/">Version 1.0.0</a>', start: '2018-05-13'},
]);

// Configuration for the Timeline
var options = {
stack: true,
tooltip: {
//       followMouse: true,
//       overflowMethod: 'cap'
}
};

// Create a Timeline
var timeline = new vis.Timeline(container, items, options);
</script>


## Release v1.0.0


You might ask why it is released now. Our experience in various deployments in production shows Colly provides a stable and robust platform for developing and running scrapers both locally and in multi server configuration. The feature set is complete and ready to support even complex use cases. What are those features?

* **Rate limiting** During scraping controlling the number of request sent to the scraped site might be crucial. We would not want to disrupt the service by overloading with too many requests. It is bad for the operators of the site and also for us, because the data we would like to collect becomes inaccessible. Thus, request number must be limited. The collector provided by Colly can be configured to send only a limited number of requests in parallel.

* **Request caching** To relieve the load from external services and decrease the number of outgoing requests response caching is supported.

* **Configurable via environment variables** To eliminate rebuilding of your scraper during fine-tuning, Colly can read configuration options from environment variables. So you can modify its settings without a Golang development environment.

* **Proxies/proxy switchers** If the address of scrapers has to be hidden proxies can be added to make requests instead of the machine running the scraping job. Furthermore, to scale Colly without running multiple scraper instances, proxy switchers can be used. Collectors support proxy switchers which can distribute requests among multiple servers. Scraping collected sites is still done on the machine running the scrapers. But the network traffic is moved to different hosts.

* **Storage backend and storage interface** During scraping a various data needs to be stored and sometimes shared. To access these objects Colly provides a storage interface. You can create your own storages and use it in your scraper by implementing the interface required. By default Colly saves everything into memory. Additional Colly backend implementations are available for Redis and SQLite3.

* **Request queue** Scraping pages in parallel asynchronously is a must have feature when scraping. Colly maintains a request queue where URLs found during scraping are collected. Worker threads of your collector are taking these URLs and creating requests.

* **Goodies** The package named `extensions` provides multiple helpers for collectors. These are common functions implemented in advance, so you don't have to bloat your scraper code with general implementations. An example extension is `RandomUserAgent` which generates a random User Agent for every request. You can find the full list of Goodies: https://godoc.org/github.com/gocolly/colly/extensions

* **Debuggers** Debugging can be painful. Colly tries to ease the pain by providing `Debuggers` to inspect your scraper. You can simply write debug messages to the console by using `LogDebugger`. If you prefer web interfaces, we've got you covered. Colly comes with a web debugger. You can use it by initializing a `WebDebugger`. See here how debuggers can be used: https://godoc.org/github.com/gocolly/colly/debug

We the team behind Colly believe that it has become a stable and mature scraping framework capable of supporting complex use cases. We are hoping for an even more productive future. Last but not least **thank you for your support and contributions**.
