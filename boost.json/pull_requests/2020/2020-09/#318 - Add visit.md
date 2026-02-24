# #318 Add visit [Closed]

> Username: vinniefalco  
> Created at: 2020-09-11 19:16:44 UTC  
> Updated at: 2020-10-04 21:04:41 UTC  
> Closed at: 2020-10-04 21:04:40 UTC  
> Url: https://github.com/boostorg/json/pull/318  

fix #207

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-09-11 19:25:42 UTC  
> Url: https://github.com/boostorg/json/pull/318#issuecomment-691272737  

An automated preview of the documentation is available at [http://318.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://318.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Review 2 [Commented]

> Username: sdkrystian  
> Created_at: 2020-09-11 20:07:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/318#pullrequestreview-487092742  

📁 include/boost/json/visit.hpp

```diff
  30 |+     Visitor&& v,
  31 |+     value&& jv) -> decltype(
  32 |+         std::declval<Visitor>()(nullptr))
```

> Username: sdkrystian  
> Created_at: 2020-09-11 20:07:51 UTC  
> Url: https://github.com/boostorg/json/pull/318#discussion_r487265731  

Do `std::forward<Visitor>(v)`


---

## Review 3 [Commented]

> Username: sdkrystian  
> Created_at: 2020-09-11 20:08:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/318#pullrequestreview-487092844  

📁 include/boost/json/visit.hpp

```diff
  35 |+     {
  36 |+     default: // unreachable()?
  37 |+     case kind::array:   return v(jv.get_array());
```

> Username: sdkrystian  
> Created_at: 2020-09-11 20:08:01 UTC  
> Url: https://github.com/boostorg/json/pull/318#discussion_r487265798  

Same thing here


---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2020-09-11 20:51:32 UTC  
> Url: https://github.com/boostorg/json/pull/318#issuecomment-691306162  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/318/pullrequest-condensed-c8f1253.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/318/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/318/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2020-10-04 02:08:15 UTC  
> Url: https://github.com/boostorg/json/pull/318#issuecomment-703188871  

An automated preview of the documentation is available at [https://318.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://318.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2020-10-04 17:53:34 UTC  
> Url: https://github.com/boostorg/json/pull/318#issuecomment-703291358  

An automated preview of the documentation is available at [https://318.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://318.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2020-10-04 21:04:40 UTC  
> Url: https://github.com/boostorg/json/pull/318#issuecomment-703315978  

Merged

---
