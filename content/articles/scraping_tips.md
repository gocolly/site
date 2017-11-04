---
title: "Web Scraping Tips and Tricks"
description: "Tips and tricks no one tells you"
date: 2017-11-04
author: kvch
---

Web scraping is extracting data from websites by getting them and selecting the relevant parts and present it in a readable or parseable format.

After you decided which site or sites you want to collect data from, check if an API is available. If possible, use it.  However, if no public API is available or is not sufficient, you can turn to scraping.

But how to collect data from websites and create scrapers which get the job done smoothly?

## Source discovery

First, you need to specify what parts of a sites contain relevant data. Selectors can be used to query different elements of a HTML page. HTML source viewers help you come up with sufficient selectors. A simple HTML source viewer is available in browsers. Simply right click in the page and select "View page source" from the options.

{{<tip>}}Always use the source code view of a page. The DOM displayed in browser inspectors include the changes done by Javascript files.{{</tip>}}

If you prefer working form the terminal instead of browsers, `wuzz` is the right tool for you. It is an interactive HTTP inspector with a terminal UI. You can learn about it more at his GitHub page: https://github.com/asciimoo/wuzz. Displays the both the response body and headers from a site and the contents can be searched. Inspecting headers is an important part of crafting requests to get the desired data. Some services require headers and cookies to return responses as seen in browsers.

{{<tip>}}If you are not recieving the same result using a script from a service, try adding HTTP headers to your requests.{{</tip>}}

### JS-only web pages

I am dedicating a complete subsection to JS-only web pages, as there are a few misconceptions regarding them. JS pages, are sites which require Javascript to display its pages. However, to get the data, it is not reqiured to run PhantomJS or other headless webkits. As these scripts get their data from a backend, you must discover and inspect the endpoints scripts use. Discovering these endpoints can be done using the "Network" pane of "Developer tools" in browsers. Load the page and view what requests are made to what sources. Inspect the content of the responses and find the request which retrieves the data you need. As these requests are done by JS, there is a good chance that responses are in JSON. If you have JSON responses there is no need to come up with selectors, you can easily parse those responses.

{{<tip>}}Discover internal APIs using the "Network" pane of developer tools of browsers.{{</tip>}}

### Selectors

Coming up futureproof and simple selectors can be a challenging. But there are a few best practices you can follow.

Look for unique identifiers in the source. For example in sidebars and actual content are separeted. Actual posts or content reside under a uniquely identified element e.g. `<div id="content">`. These elements are tend to be persistent and contain the data in the future.

Furthermore, you can get rid of redundant parts of selectors by looking for unique names.

{{<tip>}}Use minimal unique identifiers.{{</tip>}}

If the source is difficult to parse, try viewing it without Javascript. Sites can still show you the information, but without fancy UI elements which only gets in your way during scraping. `noscript` tags usually contain the information in more parseable format and with less noise. Or it might redirect you to a static version of the page, which is also easy to parse.

{{<tip>}}View the site without Javascript.{{</tip>}}

## Leave as minimal footprint as possible

To avoid hitting rate limits or disrupting services, do not make unnecessary requests. 

{{<tip>}}Add delays between requests.{{</tip>}}

If you are traversing multiple pages, it is a good idea to create whitelists or blacklists to differentiate between relevant and irrelevant pages. Thus, the number of pages are limited only to the necessary ones.

{{<tip>}}Do not visit pages you are not interested in.{{</tip>}}

{{<tip>}}Cache requests.{{</tip>}}

## References

- https://github.com/asciimoo/wuzz
