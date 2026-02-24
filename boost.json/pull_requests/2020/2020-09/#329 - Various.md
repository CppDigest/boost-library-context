# #329 Various [Merged]

> Username: vinniefalco  
> Created at: 2020-09-13 19:54:48 UTC  
> Updated at: 2020-09-13 22:42:14 UTC  
> Merged at: 2020-09-13 21:57:14 UTC  
> Closed at: 2020-09-13 21:57:14 UTC  
> Url: https://github.com/boostorg/json/pull/329  



---

## Review 1 [Commented]

> Username: cdesouza-chromium  
> Created_at: 2020-09-13 20:51:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/329#pullrequestreview-487338881  

📁 include/boost/json/parser.hpp

```diff
  40 |-     returns `true`, or when a non-successful error
  41 |-     code is returned.
  32 |+     Parsing for a new JSON may begin the parser is
```

> Username: cdesouza-chromium  
> Created_at: 2020-09-13 20:51:39 UTC  
> Updated_at: 2020-09-13 21:24:55 UTC  
> Url: https://github.com/boostorg/json/pull/329#discussion_r487575932  

This sentence looks broken... possibly meant to say 'when the parser is'

> Username: vinniefalco  
> Created_at: 2020-09-13 21:17:37 UTC  
> Updated_at: 2020-09-13 21:24:55 UTC  
> Url: https://github.com/boostorg/json/pull/329#discussion_r487578482  

This will be fixed elsewhere

> Username: cdesouza-chromium  
> Created_at: 2020-09-13 21:47:49 UTC  
> Updated_at: 2020-09-13 21:48:00 UTC  
> Url: https://github.com/boostorg/json/pull/329#discussion_r487581368  

Ack


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2020-09-13 21:11:17 UTC  
> Url: https://github.com/boostorg/json/pull/329#issuecomment-691725997  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/329/pullrequest-condensed-bff676b.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/329/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/329/pullrequest.html)

---

## Review 3 [Commented]

> Username: cdesouza-chromium  
> Created_at: 2020-09-13 21:19:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/329#pullrequestreview-487339514  

📁 include/boost/json/parser.hpp

```diff
 235 |-         std::size_t temp_size) noexcept;
 192 |+         unsigned char* buffer,
 193 |+         std::size_t size) noexcept;
```

> Username: cdesouza-chromium  
> Created_at: 2020-09-13 21:01:31 UTC  
> Updated_at: 2020-09-13 21:24:55 UTC  
> Url: https://github.com/boostorg/json/pull/329#discussion_r487576938  

Have you considered using a string_view here... or is it just a matter of making calls using `std::byte*` less clunky?

> Username: sdkrystian  
> Created_at: 2020-09-13 21:20:51 UTC  
> Updated_at: 2020-09-13 21:24:55 UTC  
> Url: https://github.com/boostorg/json/pull/329#discussion_r487578783  

`string_view` is non-mutable

> Username: cdesouza-chromium  
> Created_at: 2020-09-13 21:47:31 UTC  
> Updated_at: 2020-09-13 21:48:00 UTC  
> Url: https://github.com/boostorg/json/pull/329#discussion_r487581353  

Oh, my bad, I made the wrong assumption here... I guess that a span-like type could be useful here then, but if you don't have any such construct in this project, it doesn't pay off to bring it from another dependency.


---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2020-09-13 22:11:46 UTC  
> Url: https://github.com/boostorg/json/pull/329#issuecomment-691732633  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/329/pullrequest-condensed-a819af8.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/329/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/329/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2020-09-13 22:42:14 UTC  
> Url: https://github.com/boostorg/json/pull/329#issuecomment-691735743  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/329/pullrequest-condensed-7072a6f.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/329/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/329/pullrequest.html)

---
