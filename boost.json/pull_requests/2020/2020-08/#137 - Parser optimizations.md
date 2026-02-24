# #137 Parser optimizations [Closed]

> Username: sdkrystian  
> Created at: 2020-08-08 04:33:26 UTC  
> Updated at: 2020-08-22 18:03:26 UTC  
> Closed at: 2020-08-09 22:06:22 UTC  
> Url: https://github.com/boostorg/json/pull/137  



---

## Comment 1

> Username: sdkrystian  
> Created_at: 2020-08-08 07:06:39 UTC  
> Url: https://github.com/boostorg/json/pull/137#issuecomment-670836287  

This PR was rebased on top of #136, so really only the 5 most recent commits should be merged

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-09 15:50:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/137#pullrequestreview-463884423  

📁 include/boost/json/detail/basic_parser.hpp

```diff
 134 |+ 
 135 |+     BOOST_NOINLINE
 136 |+     inline
```

> Username: vinniefalco  
> Created_at: 2020-08-09 15:50:20 UTC  
> Updated_at: 2020-08-09 21:45:16 UTC  
> Url: https://github.com/boostorg/json/pull/137#discussion_r467599153  

why `BOOST_NOINLINE` followed by `inline` ?

> Username: sdkrystian  
> Created_at: 2020-08-09 19:31:08 UTC  
> Updated_at: 2020-08-09 21:45:16 UTC  
> Url: https://github.com/boostorg/json/pull/137#discussion_r467620061  

We want the ODR benefits of the `inline` keyword (multiple definitions), but we don't actually want the function to be inlined.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-09 15:53:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/137#pullrequestreview-463884614  

📁 include/boost/json/detail/basic_parser.hpp

```diff
 202 |-     template<bool StackEmpty, bool AllowInvalid, class Handler>
 201 |+     template<bool StackEmpty, bool IsKey,
 202 |+         bool AllowInvalid, class Handler>
```

> Username: vinniefalco  
> Created_at: 2020-08-09 15:53:08 UTC  
> Updated_at: 2020-08-09 21:45:16 UTC  
> Url: https://github.com/boostorg/json/pull/137#discussion_r467599426  

I think we should rename `AllowInvalid` to `AllowBadUTF8`. This change should be in its own commit.


---

## Comment 4

> Username: vinniefalco  
> Created_at: 2020-08-09 15:59:01 UTC  
> Url: https://github.com/boostorg/json/pull/137#issuecomment-671069067  

I think the "Better key parsing" commit should be removed entirely. It is an optimization, and I do not want to merge any more optimizations, there have been enough already. Any new optimizations should be submitted only when the following conditions have all been met:  
1. Boost.JSON is accepted in review,  
2. There have been no logic or feature changes merged for at least 60 days, and  
3. The change is accompanied by comprehensive benchmarks in a stable environment which demonstrate the benefit.  
4. A measurement is provided of the change in the size of the emitted binary with optimizations turned on.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2020-08-09 22:06:22 UTC  
> Url: https://github.com/boostorg/json/pull/137#issuecomment-671106562  

Too many changes and too many unnecessary changes, sorry

---
