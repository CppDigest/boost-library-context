# #460 Refactor CI [Closed]

> Username: sdkrystian  
> Created at: 2020-10-26 15:54:10 UTC  
> Updated at: 2020-10-30 18:48:10 UTC  
> Closed at: 2020-10-30 18:48:10 UTC  
> Url: https://github.com/boostorg/json/pull/460  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-10-26 17:08:44 UTC  
> Url: https://github.com/boostorg/json/pull/460#issuecomment-716693656  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/460/pullrequest-condensed-a30048f.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/460/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/460/pullrequest.html)

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-10-26 20:26:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/460#pullrequestreview-517148221  

📁 include/boost/json/value.hpp

```diff
2481 |                 T>::type
2482 |     {
2483 |+         using limits = std::numeric_limits<T>;
```

> Username: vinniefalco  
> Created_at: 2020-10-26 20:26:49 UTC  
> Updated_at: 2020-10-29 22:33:12 UTC  
> Url: https://github.com/boostorg/json/pull/460#discussion_r512246577  

Is the alias necessary for fixing the warnings?

> Username: vinniefalco  
> Created_at: 2020-10-27 03:21:19 UTC  
> Updated_at: 2020-10-29 22:33:12 UTC  
> Url: https://github.com/boostorg/json/pull/460#discussion_r512393433  

If you are going to change it use `DBL_MIN` and `DBL_MAX` or whatever those macros are called

> Username: sdkrystian  
> Created_at: 2020-10-28 15:54:26 UTC  
> Updated_at: 2020-10-29 22:33:12 UTC  
> Url: https://github.com/boostorg/json/pull/460#discussion_r513559598  

The alias isn't necessary, I just added it for convenience. We can use the macros instead


---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2020-10-26 21:18:39 UTC  
> Url: https://github.com/boostorg/json/pull/460#issuecomment-716829605  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/460/pullrequest-condensed-f4cc1b3.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/460/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/460/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2020-10-28 18:58:40 UTC  
> Url: https://github.com/boostorg/json/pull/460#issuecomment-718142616  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/460/pullrequest-condensed-6d5fdac.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/460/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/460/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2020-10-29 23:48:41 UTC  
> Url: https://github.com/boostorg/json/pull/460#issuecomment-719087240  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/460/pullrequest-condensed-f10e62c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/460/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/460/pullrequest.html)

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2020-10-30 18:48:10 UTC  
> Url: https://github.com/boostorg/json/pull/460#issuecomment-719733459  

Merged, thanks

---
