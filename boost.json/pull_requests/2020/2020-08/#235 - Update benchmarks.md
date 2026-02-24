# #235 Update benchmarks [Closed]

> Username: sdkrystian  
> Created at: 2020-08-30 02:43:27 UTC  
> Updated at: 2020-08-30 23:41:05 UTC  
> Closed at: 2020-08-30 23:41:05 UTC  
> Url: https://github.com/boostorg/json/pull/235  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-08-30 02:51:21 UTC  
> Url: https://github.com/boostorg/json/pull/235#issuecomment-683369043  

An automated preview of the documentation is available at [http://235.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://235.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-30 03:10:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/235#pullrequestreview-478167978  

📁 doc/qbk/05_performance.qbk

```diff
   8 |+ ]
   9 |+ 
  10 |+ [section Performance]
```

> Username: vinniefalco  
> Created_at: 2020-08-30 03:10:02 UTC  
> Url: https://github.com/boostorg/json/pull/235#discussion_r479714861  

when you rename sections you break all the hyperlinks.  
This is the current link:  
```  
http://vinniefalco.github.io/doc/json/json/benchmarks.html  
```  
And this will be the link with your changes:  
```  
http://vinniefalco.github.io/doc/json/json/performance.html  
```  
So... all those reddit posts, Twitter posts, github issues in foreign repos, Boost mailing list posts, etc... that link to the benchmarks.html page will have broken links.

> Username: sdkrystian  
> Created_at: 2020-08-30 03:12:23 UTC  
> Url: https://github.com/boostorg/json/pull/235#discussion_r479715007  

Yes, that is why I did this before release. I don't think that there are too many links out there anyways, but we can always add a redirect anyways.

> Username: vinniefalco  
> Created_at: 2020-08-30 03:15:28 UTC  
> Url: https://github.com/boostorg/json/pull/235#discussion_r479715215  

What is wrong with "Benchmarks?"

> Username: sdkrystian  
> Created_at: 2020-08-30 03:23:54 UTC  
> Url: https://github.com/boostorg/json/pull/235#discussion_r479715694  

It doesn't leave much room for expanding the section in the future.

> Username: vinniefalco  
> Created_at: 2020-08-30 03:25:15 UTC  
> Url: https://github.com/boostorg/json/pull/235#discussion_r479715807  

Expanding how, to talk about performance? Some kind of exposition? How we achieved our numbers?

> Username: sdkrystian  
> Created_at: 2020-08-30 03:25:26 UTC  
> Url: https://github.com/boostorg/json/pull/235#discussion_r479715821  

Yes

> Username: vinniefalco  
> Created_at: 2020-08-30 03:49:20 UTC  
> Url: https://github.com/boostorg/json/pull/235#discussion_r479717403  

If you're going to write exposition it should go in another section. So you can leave this one at "Benchmarks", and later add a "Performance" section where you have a larger discussion.

> Username: vinniefalco  
> Created_at: 2020-08-30 23:40:50 UTC  
> Updated_at: 2020-08-30 23:40:51 UTC  
> Url: https://github.com/boostorg/json/pull/235#discussion_r479830766  

Yes I see, when the time comes we can change "Benchmarks" in the top level index to "Performance", put the exposition there, and the current "Benchmarks" page will be a sub-section on its own page (and thus preserve the Link). When we are ready to add the exposition I will show you how to structure the documents.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-30 03:15:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/235#pullrequestreview-478168167  

📁 doc/qbk/05_performance.qbk

```diff
  13 |+ important libraries of interest, RapidJSON which is known for its
  14 |+ considerable performance and nlohmann which underperforms but is
  15 |+ feature-rich. The bench program measures the throughput of serialization
```

> Username: vinniefalco  
> Created_at: 2020-08-30 03:15:06 UTC  
> Updated_at: 2020-08-30 03:15:07 UTC  
> Url: https://github.com/boostorg/json/pull/235#discussion_r479715192  

non-sterile (and yes I wrote it)


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-30 03:19:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/235#pullrequestreview-478168380  

📁 doc/qbk/05_performance.qbk

```diff
 191 |+ Thanks to SSE2 optimized string processing algorithms from Peter Dimov,
 192 |+ Boost.JSON significantly outperforms all other libraries for parsing
 193 |+ and serializing unescaped strings:
```

> Username: vinniefalco  
> Created_at: 2020-08-30 03:19:57 UTC  
> Url: https://github.com/boostorg/json/pull/235#discussion_r479715490  

non-sterile (yes I wrote it). we can mention this but it should be sterile, e.g.  
  
> Boost.JSON uses SSE2 to accelerate processing of unescaped strings

> Username: sdkrystian  
> Created_at: 2020-08-30 03:23:28 UTC  
> Url: https://github.com/boostorg/json/pull/235#discussion_r479715677  

We use SSE2 for much more than just that

> Username: vinniefalco  
> Created_at: 2020-08-30 03:48:35 UTC  
> Url: https://github.com/boostorg/json/pull/235#discussion_r479717350  

Yes I know, I'm not saying to use my text verbatim. You can just remove my text (Lines 189-193), and add something later if you think of something that is relevant and sterile.

> Username: vinniefalco  
> Created_at: 2020-08-30 23:39:47 UTC  
> Updated_at: 2020-08-30 23:39:48 UTC  
> Url: https://github.com/boostorg/json/pull/235#discussion_r479830648  

I edited it myself to remove my non-sterile language


---

## Comment 5

> Username: vinniefalco  
> Created_at: 2020-08-30 23:41:05 UTC  
> Url: https://github.com/boostorg/json/pull/235#issuecomment-683486534  

This is merged

---
