---
title: "Web Scraping Tips and Tricks"
description: "Tips and tricks no one tells you"
date: 2017-11-04
author: kvch
---

Web scraping is extracting data from websites by getting them and selecting the relevant parts and present it in a readable or parsable format.

After you decided which site or sites you want to collect data from, check if an API is available. If possible, use it.  However, if no public API is available or is not sufficient, you can turn to scraping.

But how to collect data from websites and create scrapers which get the job done smoothly?

## Source discovery

First, you need to specify what parts of sites contain relevant data. Selectors can be used to query different elements of a HTML page. The two most popular selector languages are [XPath](https://www.w3schools.com/xml/xpath_syntax.asp) and [CSS Selector](https://www.w3schools.com/cssref/css_selectors.asp). HTML source viewers help you come up with appropriate selectors. A simple HTML source viewer is available in browsers. Simply right click in the page and select "View page source" from the options.

The DOM displayed in browser inspectors include the changes done by JavaScript files. So it usually differs from the actual response you need to parse.

{{<tip>}}Always use the source code view of a page.{{</tip>}}

If you prefer working form the terminal instead of browsers, `wuzz` is the right tool for you. It is an interactive HTTP inspector with a terminal UI. You can learn about it more at its [GitHub page](https://github.com/asciimoo/wuzz).

It displays both response body and headers from a site and the contents can be searched. Inspecting headers is an important part of crafting requests to get the desired data. Some services require headers and cookies to return responses as seen in browsers.

{{<tip>}}If you are not receiving the same result using a script from a service, try adding HTTP headers to your requests.{{</tip>}}

### JS-only web pages

I am dedicating a complete subsection to JS-only web pages, as there are a few misconceptions regarding them. JS pages are sites which require JavaScript to display its contents. However, to get the data, it is not required to run PhantomJS or other headless webkits. As these scripts get their data from a backend, you must discover and inspect the endpoints scripts use.

Discovering these endpoints can be done using the "Network" pane of "Developer Tools" in browsers. Load the page and view what requests are made to what sources. Inspect the content of the responses and find the request which retrieves the data you need. As these requests are done by JS, there is a good chance that responses are in JSON. If you have JSON responses there is no need to come up with selectors, you can easily parse those responses.

{{<tip>}}Discover internal APIs using the Network pane of Developer Tools of browsers.{{</tip>}}

### Selectors

Coming up future proof and simple selectors can be a challenging. But there are a few best practices you can follow.

Look for unique identifiers in the source. For example, sidebars and content are usually separated. Actual posts or content might reside under a uniquely identified element e.g. `<div id="content">`. These elements are tend to be persistent and contain the data in the future. Furthermore, you can get rid of redundant parts of selectors by looking for unique names.

{{<tip>}}Use minimal unique identifiers.{{</tip>}}

If the source is difficult to parse, try viewing it without JavaScript. Sites can still show you the information, but without fancy UI elements which only gets in your way during scraping. `noscript` tags can contain the information in more parsable format and with less noise. Or it might redirect you to a static version of the page, which is also easy to parse (e.g. Google Groups).

{{<tip>}}View the site without JavaScript.{{</tip>}}

## Leave as minimal footprint as possible

It is important not to interfere with users who want to use the scraped website in a regular way by viewing it in a browser. Some websites adopted rate limiting or just simply ban IP addresses which are making too many requests in a short period of time. However, there are still sites which are not protected and cannot handle big loads. Inaccessible services means inaccessible information, so scraping fails. To avoid hitting rate limits or disrupting services, do not make unnecessary and too many requests.

Waiting for a few seconds between requests is useful, because it lets services process requests without being overloaded. Also, it makes your script seem more human, so it does not stand out in ordinary traffic.

{{<tip>}}Add delays between requests.{{</tip>}}

If you are traversing multiple pages, it is a good idea to create whitelists or blacklists to differentiate between relevant and irrelevant pages. Thus, the number of pages are limited only to the necessary ones.

For instance, you are scraping a web shop under the domain `https://shop.com/` and you are only interested in books under `https://shop.com/books/`. Webpages of books might link to different products such as notebooks or pens. To avoid visiting those pages, a whitelist can be created which includes one pattern: `https://shop.com/books/*`. Hence, only pages containing information on books are requested from the domain.

{{<tip>}}Do not visit pages you are not interested in.{{</tip>}}

Furthermore, it is possible that multiple pages link to the same URL. There is no point in requesting it again as it has been visited previously. To eliminate duplicated requests, use a cache to store URLs which has been visited already. In smaller scraping jobs simple in-memory key value stores or lists can do the trick. If you are running your scraper for so long that visited pages might change, you can invalidate the contents of your cache.

{{<tip>}}Cache responses.{{</tip>}}

## References

- https://www.w3schools.com/xml/xpath_syntax.asp
- https://www.w3schools.com/cssref/css_selectors.asp
- https://github.com/asciimoo/wuzz
