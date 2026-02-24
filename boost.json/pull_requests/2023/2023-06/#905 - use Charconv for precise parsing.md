# #905 use Charconv for precise parsing [Merged]

> Username: grisumbras  
> Created at: 2023-06-08 13:24:52 UTC  
> Updated at: 2023-06-13 15:05:40 UTC  
> Merged at: 2023-06-13 14:21:05 UTC  
> Closed at: 2023-06-13 14:21:05 UTC  
> Url: https://github.com/boostorg/json/pull/905  



---

## Review 1 [Commented]

> Username: mborland  
> Created_at: 2023-06-08 13:32:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/905#pullrequestreview-1469928810  

📁 include/boost/json/detail/charconv/detail/config.hpp

```diff
  20 |+ #else
  21 |+ #  define BOOST_JSON_CXX14_CONSTEXPR inline
  22 |+ #endif
```

> Username: mborland  
> Created_at: 2023-06-08 13:32:27 UTC  
> Updated_at: 2023-06-08 13:32:41 UTC  
> Url: https://github.com/boostorg/json/pull/905#discussion_r1223050032  

```diff  
+#ifndef BOOST_NO_CXX14_CONSTEXPR  
+#  define BOOST_JSON_CXX14_CONSTEXPR BOOST_CXX14_CONSTEXPR  
+#  define BOOST_JSON_CXX14_CONSTEXPR_NO_INLINE BOOST_CXX14_CONSTEXPR  
+#else  
+#  define BOOST_JSON_CXX14_CONSTEXPR inline  
+#  define BOOST_JSON_CXX14_CONSTEXPR_NO_INLINE  
+#endif  
```  
  
You'll need the no inline version in fast float because I think everything marked `CXX14_CONSTEXPR` is also `BOOST_FORCEINLINE`. They'll collide with C++11

> Username: grisumbras  
> Created_at: 2023-06-08 13:34:17 UTC  
> Url: https://github.com/boostorg/json/pull/905#discussion_r1223052085  

Indeed


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-06-08 16:38:04 UTC  
> Url: https://github.com/boostorg/json/pull/905#issuecomment-1583006899  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/905/pullrequest-condensed-ae94b4a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/905/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/905/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-06-13 12:30:37 UTC  
> Url: https://github.com/boostorg/json/pull/905#issuecomment-1589211470  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/905/pullrequest-condensed-dcd4f73.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/905/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/905/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-06-13 15:05:40 UTC  
> Url: https://github.com/boostorg/json/pull/905#issuecomment-1589498236  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/905/pullrequest-condensed-ac11377.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/905/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/905/pullrequest.html)

---
