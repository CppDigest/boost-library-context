# #322 Review docs [Closed]

> Username: sdkrystian  
> Created at: 2020-09-12 04:12:12 UTC  
> Updated at: 2020-10-05 21:38:30 UTC  
> Closed at: 2020-10-05 21:38:30 UTC  
> Url: https://github.com/boostorg/json/pull/322  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-09-12 07:12:48 UTC  
> Url: https://github.com/boostorg/json/pull/322#issuecomment-691431808  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/322/pullrequest-condensed-d7a3070.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/322/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/322/pullrequest.html)

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-09-14 20:59:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/322#pullrequestreview-488136751  

📁 include/boost/json/value.hpp

```diff
2217 | 
2218 |-         If @ref is_int64() is `true`, returns
2218 |+         If @ref is_uint64() is `true`, returns
```

> Username: vinniefalco  
> Created_at: 2020-09-14 20:59:22 UTC  
> Url: https://github.com/boostorg/json/pull/322#discussion_r488216416  

`is_uint64` returns a pointer not a bool, same with the others

> Username: sdkrystian  
> Created_at: 2020-09-14 23:37:33 UTC  
> Url: https://github.com/boostorg/json/pull/322#discussion_r488295959  

How should it be worded then? "If is_uint64_t() returns a non-null pointer"?

> Username: vinniefalco  
> Created_at: 2020-09-14 23:39:20 UTC  
> Url: https://github.com/boostorg/json/pull/322#discussion_r488296441  

actually we are going to roll back the pointer change, stay tuned


---

## Comment 3

> Username: vinniefalco  
> Created_at: 2020-09-16 16:19:26 UTC  
> Url: https://github.com/boostorg/json/pull/322#issuecomment-693513994  

This needs rebasing, since I refactored the `is_array` interfaces

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2020-10-05 21:38:30 UTC  
> Url: https://github.com/boostorg/json/pull/322#issuecomment-703903410  

This was never rebased and thus cannot be merged.

---
