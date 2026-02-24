# #300 Add detail::unreachable [Closed]

> Username: sdkrystian  
> Created at: 2020-09-07 21:34:28 UTC  
> Updated at: 2020-09-08 02:05:45 UTC  
> Closed at: 2020-09-08 02:05:45 UTC  
> Url: https://github.com/boostorg/json/pull/300  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-09-07 21:48:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/300#pullrequestreview-483714637  

📁 include/boost/json/detail/config.hpp

```diff
 223 |+ BOOST_FORCEINLINE
 224 |+ void
 225 |+ unreachable()
```

> Username: vinniefalco  
> Created_at: 2020-09-07 21:48:04 UTC  
> Updated_at: 2020-09-07 21:48:05 UTC  
> Url: https://github.com/boostorg/json/pull/300#discussion_r484572814  

doesn't this need to be marked `inline`?

> Username: sdkrystian  
> Created_at: 2020-09-07 22:20:36 UTC  
> Url: https://github.com/boostorg/json/pull/300#discussion_r484577007  

`BOOST_FORCEINLINE` does that for us

> Username: sdkrystian  
> Created_at: 2020-09-07 22:20:42 UTC  
> Updated_at: 2020-09-07 22:20:43 UTC  
> Url: https://github.com/boostorg/json/pull/300#discussion_r484577024  

`BOOST_FORCEINLINE` does that for us


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2020-09-07 23:59:30 UTC  
> Url: https://github.com/boostorg/json/pull/300#issuecomment-688546829  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/300/pullrequest-condensed.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/300/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/300/pullrequest.html)

---
