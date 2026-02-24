# #455 Add hash example [Closed]

> Username: madmongo1  
> Created at: 2020-10-22 06:20:35 UTC  
> Updated at: 2023-01-02 10:31:11 UTC  
> Closed at: 2023-01-02 10:31:10 UTC  
> Url: https://github.com/boostorg/json/pull/455  

documentation embedding will need a review

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-10-22 06:26:46 UTC  
> Url: https://github.com/boostorg/json/pull/455#issuecomment-714261742  

An automated preview of the documentation is available at [https://455.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://455.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2020-10-22 06:56:36 UTC  
> Url: https://github.com/boostorg/json/pull/455#issuecomment-714275453  

An automated preview of the documentation is available at [https://455.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://455.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2020-10-22 09:14:18 UTC  
> Url: https://github.com/boostorg/json/pull/455#issuecomment-714353284  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/455/pullrequest-condensed-ca30d0f.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/455/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/455/pullrequest.html)

---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2020-10-22 11:20:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/455#pullrequestreview-514618658  

📁 example/hash.cpp

```diff
  90 |+ {
  91 |+     std::size_t                                   seed = 0;
  92 |+     thread_local std::vector< json::string_view > keys;
```

> Username: pdimov  
> Created_at: 2020-10-22 11:20:37 UTC  
> Url: https://github.com/boostorg/json/pull/455#discussion_r510081297  

No need for thread_local on a local variable.

> Username: vinniefalco  
> Created_at: 2020-10-22 12:14:09 UTC  
> Url: https://github.com/boostorg/json/pull/455#discussion_r510110825  

I think he meant to wrtite `thread_local static`


---

## Comment 5

> Username: grisumbras  
> Created_at: 2023-01-02 10:31:10 UTC  
> Url: https://github.com/boostorg/json/pull/455#issuecomment-1368817730  

Implemented as a library feature

---
