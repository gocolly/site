---
title: "How to Scrape Instagram Profiles"
description: "Download all the pictures uploaded to an Instagram profile"
date: 2017-11-10
author: asciimoo
---

Scraping can be tedious work especially if the target site isn't just a standard static HTML page. Plenty of modern sites have JavaScript only UIs where extracting content is not always trivial. Instagram is one of these websites, so I would like to show you how it is possible to write a scraper relatively fast to get images from Instagram. The full working example can be found [here](/docs/examples/instagram/).


## Information gathering


First, if we view the source code of a profile page (e.g. https://instagram.com/instagram), we can see a bunch of JavaScript code inside the `body` tag instead of static HTML tags. Let's take a closer look at it. We can see that the first `script` is just a variable declaration where a huge JSON is assigned to a single variable (`window._sharedData`). This JSON can be easily extracted from the `script` tag by finding the first `{` character and getting the whole content after it:

```go
jsonData := scriptContent[strings.Index(scriptContent, "{") : len(scriptContent)-1]
```

Note that because it is a JavaScript variable declaration it has a trailing semicolon what we have to cut off to get a valid JSON. That's why the example above ends with `len(scriptContent)-1`.

The formatted view of the extracted JSON reveals all the information we are looking for. The JSON contains information about a user's images and some metadata of the profile (e.g. the profile ID is `25025320`). There is an interesting part of the metadata called `page_info`:

```json
 "page_info": {
  "has_next_page": true,
  "end_cursor": "AQBiQhGRC6c6f-YOxdU0ApaAvotN4zI601ymkAtQ8SutdWz2n-bKFCkv51PMAoi9im3tNDTFLyhV969z8a6JnAkQMzHbYVwNI4Ke7jbk99nvFA"
 }
```

Probably, the value of `end_cursor`' is the attribute of the URL to get the next page when `has_next_page` is `true`.

{{<tip>}}Format JSONs with the handy <a href="https://github.com/stedolan/jq" target="_blank">jq</a> command line tool{{</tip>}}

### Paging

The next page of the user profile is retrieved by an AJAX call, so we have to use the browser's Network Inspector to find out what is required to fetch it. Network Inspector shows a long and cryptic URL which has two GET parameters `query_id` and `variables`:
    
```
https://www.instagram.com/graphql/query/?query_id=17888483320059182&variables=%7B%22id%22%3A%2225025320%22%2C%22first%22%3A12%2C%22after%22%3A%22AQBiQhGRC6c6f-YOxdU0ApaAvotN4zI601ymkAtQ8SutdWz2n-bKFCkv51PMAoi9im3tNDTFLyhV969z8a6JnAkQMzHbYVwNI4Ke7jbk99nvFA%22%7D
```

It seems like Instagram uses a (GraphQL)[https://en.wikipedia.org/wiki/GraphQL] API and the value of `variables` GET parameter is an URL encoded value. We can decode it with a single line of Python code:

```
$ python -c 'import urlparse;print(urlparse.parse_qs("variables=%7B%22id%22%3A%2225025320%22%2C%22first%22%3A12%2C%22after%22%3A%22AQBiQhGRC6c6f-YOxdU0ApaAvotN4zI601ymkAtQ8SutdWz2n-bKFCkv51PMAoi9im3tNDTFLyhV969z8a6JnAkQMzHbYVwNI4Ke7jbk99nvFA%22%7D")["variables"][0])'
{"id":"25025320","first":12,"after":"AQBiQhGRC6c6f-YOxdU0ApaAvotN4zI601ymkAtQ8SutdWz2n-bKFCkv51PMAoi9im3tNDTFLyhV969z8a6JnAkQMzHbYVwNI4Ke7jbk99nvFA"}
```

As you can see it is a JSON object and the value of the `after` attribute is the same as the value of the `end_cursor` and `id` is the ID of the profile.

The only unknown information in the next page URL is the `query_id` GET parameter. The HTML source code does not contain it, nor the cookies or response headers. After a little bit of digging it can be found in a static JS file included in the main page and seems it is a constant value.

The format of the response is also JSON but the structure is different from what we've found on the main page. This JSON contains the same information as the previous one, however we cannot use the same method to extract data due to structural differences.

## Building the scraper

The information gathering phase clearly shows that we need four building blocks to be able to fetch all images found on an Instagram profile. Let's do it using Colly.

### Extract and parse JSON from the main page

To extract content from HTML we need a new `Collector` which has a HTML callback to extract the JSON data from the `script` element. Specifying this callback and when it must be called can be done in `OnHTML` function of `Collector`.
The JSON can be easily converted to native Go structure using `json.Unmarshal` from the standard library.

```go
c := colly.NewCollector()

c.OnHTML("body > script:first-of-type", func(e *colly.HTMLElement) {
    // find JSON string
    jsonData := e.Text[strings.Index(e.Text, "{") : len(e.Text)-1]

    // parse JSON
    data := struct {
       EntryData struct {
           ProfilePage []struct {
               User struct {
                   Id    string `json:"id"`
                   Media struct {
                       Nodes []struct {
                           ImageURL     string `json:"display_src"`
                           ThumbnailURL string `json:"thumbnail_src"`
                           IsVideo      bool   `json:"is_video"`
                           Date         int    `json:"date"`
                           Dimensions   struct {
                               Width  int `json:"width"`
                               Height int `json:"height"`
                           }
                       }
                       PageInfo pageInfo `json:"page_info"`
                   } `json:"media"`
               } `json:"user"`
           } `json:"ProfilePage"`
       } `json:"entry_data"`
    }{}
    err := json.Unmarshal([]byte(jsonData), &data)
    if err != nil {
        log.Fatal(err)
    }

    // enumerate images
    page := data.EntryData.ProfilePage[0]
    actualUserId = page.User.Id
    for _, obj := range page.User.Media.Nodes {
        // skip videos
        if obj.IsVideo {
            continue
        }
        c.Visit(obj.ImageURL)
    }
    // ...
}
```

### Create and visit next page URLs

The format of the next page URL is fixed, so a format string can be declared which accepts the changing `id` and `after` parameters.

```go
const nextPageURLTemplate string = `https://www.instagram.com/graphql/query/?query_id=17888483320059182&variables={"id":"%s","first":12,"after":"%s"}`
```

### Parse next page JSONs

This is pretty much the same as the conversion of the main page's JSON except these responses have some different attribute names (e.g. the image url is `display_url` instead of `display_src`).

### Download and save images extracted from JSONs

After requesting images from Instagram using the `Visit` function, responses can be handled in `OnResponse`. It requires a callback as a parameter which is called after the response has arrived. To select responses which include images, we should filter based on `Content-Type` HTTP header. If it is image, it must be saved.

```go
c.OnResponse(func(r *colly.Response) {
    if strings.Index(r.Headers.Get("Content-Type"), "image") > -1 {
        r.Save(outputDir + r.FileName())
        return
    }
    // handle further response types...
}
```

## Epilogue

Scraping JS-only sites isn't always trivial, but can be handled without headless browsers and client side code execution to achieve great performance. This scraper example downloads approximately 1000 images a minute on a single thread over a regular home Internet connection.

It can be tweaked further to handle videos and extract meta information.
