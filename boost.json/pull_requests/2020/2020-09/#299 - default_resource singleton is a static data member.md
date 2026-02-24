# #299 default_resource singleton is a static data member [Closed]

> Username: sdkrystian  
> Created at: 2020-09-07 20:12:13 UTC  
> Updated at: 2020-09-09 23:45:15 UTC  
> Closed at: 2020-09-09 23:39:29 UTC  
> Url: https://github.com/boostorg/json/pull/299  



---

## Comment 1

> Username: vinniefalco  
> Created_at: 2020-09-07 20:19:25 UTC  
> Url: https://github.com/boostorg/json/pull/299#issuecomment-688498280  

needs tests for order-of-init

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2020-09-07 21:58:59 UTC  
> Url: https://github.com/boostorg/json/pull/299#issuecomment-688522141  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest-condensed.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2020-09-08 03:20:38 UTC  
> Url: https://github.com/boostorg/json/pull/299#issuecomment-688594968  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest-condensed.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest.html)

---

## Review 4 [Changes requested]

> Username: vinniefalco  
> Created_at: 2020-09-08 03:44:46 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/json/pull/299#pullrequestreview-483776480  

still needs tests for order-of-init

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-09-09 02:58:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/299#pullrequestreview-484627965  

📁 include/boost/json/impl/storage_ptr.ipp

```diff
  16 | 
  17 | // KRYSTIAN NOTE: we need P1077
  18 |+ #ifdef __clang__
```

> Username: vinniefalco  
> Created_at: 2020-09-09 02:58:50 UTC  
> Updated_at: 2020-09-09 19:28:41 UTC  
> Url: https://github.com/boostorg/json/pull/299#discussion_r485307952  

P1077 won't help us until what, C++23? Or C++26? Or never? And even then, we still have to support older versions of C++. Therefore, P1077 will never be of any help.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2020-09-09 03:00:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/299#pullrequestreview-484628493  

📁 include/boost/json/impl/storage_ptr.ipp

```diff
  38 |+ 
  39 |+ template<class T>
  40 |+ no_destroy singleton<T>::resource_;
```

> Username: vinniefalco  
> Created_at: 2020-09-09 03:00:34 UTC  
> Updated_at: 2020-09-09 19:28:41 UTC  
> Url: https://github.com/boostorg/json/pull/299#discussion_r485308444  

You are injecting all these into the `boost::json` namespace, I think they should go in `boost::json::detail`?

> Username: sdkrystian  
> Created_at: 2020-09-09 14:27:48 UTC  
> Updated_at: 2020-09-09 19:28:41 UTC  
> Url: https://github.com/boostorg/json/pull/299#discussion_r485656338  

I'll just move this to `default_resource`


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2020-09-09 03:01:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/299#pullrequestreview-484628636  

📁 include/boost/json/storage_ptr.hpp

```diff
 176 |-         : i_(0)
 172 |+         : i_(reinterpret_cast<
 173 |+             std::uintptr_t>(get_default()))
```

> Username: vinniefalco  
> Created_at: 2020-09-09 03:01:03 UTC  
> Updated_at: 2020-09-09 19:28:41 UTC  
> Url: https://github.com/boostorg/json/pull/299#discussion_r485308583  

this is GUD


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2020-09-09 03:01:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/299#pullrequestreview-484628797  

📁 include/boost/json/storage_ptr.hpp

```diff
 338 |+         i_ = detail::exchange(other.i_,
 339 |+             reinterpret_cast<
 340 |+                 std::uintptr_t>(get_default()));
```

> Username: vinniefalco  
> Created_at: 2020-09-09 03:01:37 UTC  
> Updated_at: 2020-09-09 19:28:41 UTC  
> Url: https://github.com/boostorg/json/pull/299#discussion_r485308734  

Should `get_default` maybe return `std::uintptr_t`? It would clean up these casts.

> Username: sdkrystian  
> Created_at: 2020-09-09 18:39:52 UTC  
> Updated_at: 2020-09-09 19:28:41 UTC  
> Url: https://github.com/boostorg/json/pull/299#discussion_r485833781  

Yup, did that


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2020-09-09 03:02:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/299#pullrequestreview-484629091  

📁 include/boost/json/impl/storage_ptr.ipp

```diff
  45 |+ {
  46 |+     return &singleton<void>::
  47 |+         resource_.resource_;
```

> Username: vinniefalco  
> Created_at: 2020-09-09 03:02:46 UTC  
> Updated_at: 2020-09-09 19:28:41 UTC  
> Url: https://github.com/boostorg/json/pull/299#discussion_r485309047  

`resource_.resource_` yuck

> Username: sdkrystian  
> Created_at: 2020-09-09 18:40:04 UTC  
> Updated_at: 2020-09-09 19:28:41 UTC  
> Url: https://github.com/boostorg/json/pull/299#discussion_r485833912  

Fixed


---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2020-09-09 03:45:49 UTC  
> Url: https://github.com/boostorg/json/pull/299#issuecomment-689282360  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest-condensed.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2020-09-09 04:46:19 UTC  
> Url: https://github.com/boostorg/json/pull/299#issuecomment-689298987  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest-condensed.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2020-09-09 16:18:36 UTC  
> Url: https://github.com/boostorg/json/pull/299#issuecomment-689669073  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/$pngname)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2020-09-09 17:43:33 UTC  
> Url: https://github.com/boostorg/json/pull/299#issuecomment-689716342  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/$pngname)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2020-09-09 18:43:53 UTC  
> Url: https://github.com/boostorg/json/pull/299#issuecomment-689746250  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest-condensed-0aa0288.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2020-09-09 19:44:14 UTC  
> Url: https://github.com/boostorg/json/pull/299#issuecomment-689778402  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest-condensed-2392925.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest.html)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2020-09-09 23:45:15 UTC  
> Url: https://github.com/boostorg/json/pull/299#issuecomment-689882173  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest-condensed-115ee2c.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/299/pullrequest.html)

---
