---
title: "The most important HTTP headers for scraping"
description: "Scraping related HTTP headers in depth"
date: 2018-01-01
author: asciimoo
---

What if by simply passing parameters in the URL is not enough? What can I do if I get different responses in my scraper and browser?
The answer is using and understanding HTTP headers.

HTTP headers are optional parameters of a HTTP transaction. HTTP requests and responses both can have different HTTP headers.
They allow the client and the server to pass additional information with the request or the response. A HTTP header consists of its case-insensitive name followed by a colon `:`, then by its value (without line breaks). Leading white space before the value is ignored.

![HTTP Header structure](/http_header_struct.jpg)

A detailed list of HTTP headers can be found [here](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields)

**IMPORTANT: HTTP headers are optional and can contain arbitrary data.**

{{<tip>}}Always validate every HTTP header value before using{{</tip>}}


## Request headers

These header lines are sent by the client in a HTTP protocol transaction. All lines are [RFC822](https://www.w3.org/Protocols/rfc822/3_Lexical.html#z1) format headers. The list of headers is terminated by an empty line.


### `Cookie`

HTTP cookie (web cookie, browser cookie) is a small piece of data that a server sends to the user's request. The client may store it and send it back with the next request to the same server. Typically, it's used to tell if two requests came from the same client — keeping a user logged-in, for example. It remembers stateful information for the stateless HTTP protocol.
Cookies are set by the server using `Set-Cookie`. The requests send back them using `Cookie`, so the server would know how to handle the request.

Sites often use cookies to implement authentication.

{{<tip>}}Cookies of every request in the browser can be checked using the browser's network inspector (hotkey: F12){{</tip>}}


### `User-Agent`

The `User-Agent` request header contains a characteristic string that allows the network protocol peers to identify the application type, operating system, software vendor or software version of the requesting software user agent. Validating `User-Agent` header on server side is a common operation so be sure to use valid browser's `User-Agent` string to avoid getting blocked.

Example `User-Agent`s of Firefox browsers:
```
Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0
Mozilla/5.0 (Macintosh; Intel Mac OS X x.y; rv:42.0) Gecko/20100101 Firefox/42.0
```

{{<tip>}}Change `User-Agent` strings frequently to reduce the chance of getting blocked{{</tip>}}


### `Host`

The `Host` request header specifies the domain name of the server (for virtual hosting), and (optionally) the TCP port number on which the server is listening.

A `Host` header field must be sent in all HTTP/1.1 request messages. A `400` (Bad Request) status code will be sent to any HTTP/1.1 request message that lacks a Host header field or contains more than one.


### `X-Requested-With`

Mainly used to identify AJAX requests. Most JavaScript frameworks send this field with value of `XMLHttpRequest`. Use this header to mimic browser's AJAX requests.


### `Accept-Language`

The `Accept-Language` request HTTP header advertises which languages the client is able to understand, and which locale variant is preferred. This header is a hint to be used when the server has no way of determining the language via another way, like a specific URL, that is controlled by an explicit user decision.


## Response headers

Response headers are returned by HTTP responses from the server. They might provide further metadata of the response. Their format is the same as request headers'.


### `Content-Type`

This header shows the [MIME type](https://en.wikipedia.org/wiki/Media_type) of the requested resource. It can be used to determine the response body type and encoding.


### `Content-Length`

The `Content-Length` response header field indicates the size of the response body, in decimal number of OCTETs, sent to the recipient or, in the case of the HEAD method, the size of the entity-body that would have been sent had the request been a GET. ([rfc2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html))


### `Set-Cookie`

The `Set-Cookie` HTTP response header is used to send cookies from the server to the client. When receiving an HTTP request, a server can send a Set-Cookie header with the response. The cookie is usually sent with requests made to the same server inside a Cookie HTTP header. An expiration date or duration can be specified, after which the cookie is no longer sent. Additionally, restrictions to a specific domain and path can be set, limiting where the cookie is sent.

More details about cookies can be found [here](https://en.wikipedia.org/wiki/HTTP_cookie).


## Epilogue

HTTP headers provide additional parameters to HTTP transactions. By sending the appropriate HTTP headers, one can access the response data in a different format. Or by sending cookies back to the server, sessions can be handled. Thus, making the scraper functioning.

The above headers are handled automatically by browsers, that's why it is important to know how and when to use them in scraping.

Happy scraping!
