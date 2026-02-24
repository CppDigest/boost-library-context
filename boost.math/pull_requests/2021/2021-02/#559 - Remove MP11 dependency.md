# #559 Remove MP11 dependency [Merged]

> Username: mborland  
> Created at: 2021-02-25 18:55:09 UTC  
> Updated at: 2021-08-02 11:06:32 UTC  
> Merged at: 2021-02-27 16:46:23 UTC  
> Closed at: 2021-02-27 16:46:23 UTC  
> Url: https://github.com/boostorg/math/pull/559  

Removes the only MP11 dependency. Does not need to make it in for release.

---

## Review 1 [Commented]

> Username: mborland  
> Created_at: 2021-02-25 18:57:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/559#pullrequestreview-598899824  

📁 include/boost/math/tools/mp.hpp

```diff
 341 |+ #include <utility>
 342 |+ template<std::size_t... I>
 343 |+ using index_sequence = std::index_sequence<I...>;
```

> Username: mborland  
> Created_at: 2021-02-25 18:57:25 UTC  
> Updated_at: 2021-02-26 17:51:39 UTC  
> Url: https://github.com/boostorg/math/pull/559#discussion_r583087220  

@jzmaddock `std::index_sequence` is a C++14 feature, but I didn't see anything in config that specifically called it out. Is there something better to put here?

> Username: jzmaddock  
> Created_at: 2021-02-25 19:07:58 UTC  
> Updated_at: 2021-02-26 17:51:39 UTC  
> Url: https://github.com/boostorg/math/pull/559#discussion_r583093872  

Try `defined(__cpp_lib_integer_sequence) && (__cpp_lib_integer_sequence >= 201304)`

> Username: mborland  
> Created_at: 2021-02-25 19:15:09 UTC  
> Updated_at: 2021-02-26 17:51:39 UTC  
> Url: https://github.com/boostorg/math/pull/559#discussion_r583098177  

That does the trick locally, hopefully CI agrees. Thanks.


---

## Comment 2

> Username: mborland  
> Created_at: 2021-02-27 07:27:32 UTC  
> Url: https://github.com/boostorg/math/pull/559#issuecomment-787029563  

@jzmaddock This should be good to go.

---

## Comment 3

> Username: jwakely  
> Created_at: 2021-08-02 11:06:31 UTC  
> Url: https://github.com/boostorg/math/pull/559#issuecomment-890936647  

This is badly broken, see #670

---
