---
title: "Web scraping tips and tricks"
description: "Tips and tricks no one tells you"
date: 2017-11-04
author: asciimoo
---

Web scraping is extracting data from websites by getting them and selecting the relevant parts and present it in a readable or parseable format.

After you decided which site or sites you want to collect data from, check if an API is available. If possible, use it.  However, if no public API is available or is not sufficient, you can turn to scraping.

But how to collect data from websites and create scrapers which get the job done smoothly?

## Source discovery

First, you need to specify what parts of a sites contain relevant data. As HTML are an XML based language, XPATH selectors can be used to query different elements of a HTML page. HTML source viewers help you come up with XPATH selectors. A simple HTML source viewer is available in browsers. Simply right click in the page and select "View page source" from the options. In Firefox it is possible to view the source of selected elements. All you need to do is select the interesting parts of the page, right click on them and choose "View selection source".

Tip. Always use the source code view of a page. The DOM displayed in browser inspectors lack the changes done by Javascript files.

If you prefer working form the terminal instead of browsers, `wuzz` is the right tool for you. It is an interactive HTTP inspector with a terminal UI. You can learn about it more at his GitHub page: https://github.com/asciimoo/wuzz. Displays the both the response body and headers from a site and the contents can be searched. Inspecting headers is an important part of crafting requests to get the desired data. Some services require headers and cookies to return responses as seen in browsers.

Tip. If you are not recieving the same result using a script from a service, try adding HTTP headers to your requests.

### JS-only web pages

I am dedicating a complete subsection to JS-only web pages, as there are a few misconceptions regarding them. JS pages, are sites which require Javascript to display its pages. However, to get the data, it is not reqiured to run PhantomJS or other headless webkits. As these scripts get their data from a backend, you must discover and inspect the endpoints scripts use. Discovering these endpoints can be done using the "Network" pane of "Developer tools" in browsers. Load the page and view what requests are made to what sources. Inspect the content of the responses and find the request which retrieves the data you need. As these requests are done by JS, there is a good chance that responses are in JSON. If you have JSON responses there is no need to come up with XPAH selectors, you can easily parse those responses.

Tip. Discover internal APIs using the "Network" pane of developer tools of browsers.

### Selectors

Coming up futureproof and simple selectors can be a challenging. But there are a few best practices, you can follow.

Look for unique identifiers in the source. For example in sidebars and actual content are separeted. Actual posts or content reside under a uniquely identified element e.g. `<div id="content">`. These elements are tend to be persistent and contain the data in the furute.

Tip. Use unique identifiers.

lskjdflksdjf

Tip. Write generalized selectors.

If the source is difficult to be parsed, try viewing it without Javascript. Sites can still show you the information, but without fancy UI elements which only gets in your way during scraping. `noscript` tags usually contain the information in more parseable format and with less noise.

Tip. Check the site without Javascript.

## Leave as minimal footprint as possible

To avoid hitting rate limits or disrupting services, do not make unnecessary requests. 
