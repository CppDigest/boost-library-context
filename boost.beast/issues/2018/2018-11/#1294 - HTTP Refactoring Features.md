# #1294 - HTTP Refactoring Features [Closed]

> Username: vinniefalco  
> Created at: 2018-11-09 22:03:44 UTC  
> Updated at: 2022-06-16 16:36:35 UTC  
> Closed at: 2022-06-16 16:36:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1294  

We know that the HTTP interfaces are in need of an overhaul due to problems with how the body readers and writers work. Here are some other things that should be considered:  
  
* Make it easier to forward a chunked body  
* message container should be as _Regular_ as possible  
* easier incremental parsing and serialization for buffer bodies  
* Consider `http::stream`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-11-23 18:31:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1294#issuecomment-441300504  

Added 2 items

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-06-16 16:36:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1294#issuecomment-1157889835  

This is being done, but in the new Boost.HTTP.Proto library https://github.com/CPPAlliance/http_proto
