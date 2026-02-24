# #472 X3: Improve situation with move only types [Merged]

> Username: Kojoley  
> Created at: 2019-02-26 19:23:56 UTC  
> Updated at: 2019-02-27 18:55:17 UTC  
> Merged at: 2019-02-27 16:15:17 UTC  
> Closed at: 2019-02-27 16:15:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/472  

Closes #465

---

## Comment 1

> Username: djowel  
> Created_at: 2019-02-27 15:17:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/472#issuecomment-467901483  

Nice, as usual!

---

## Review 2 [Commented]

> Username: Xeverous  
> Created_at: 2019-02-27 18:44:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/472#pullrequestreview-208697772  

📁 include/boost/spirit/home/x3/support/traits/container_traits.hpp

```diff
 132 |     {
 133 |-         return push_back_container<Container>::call(c, std::move(val));
 133 |+         return push_back_container<Container>::call(c, static_cast<T&&>(val));
```

> Username: Xeverous  
> Created_at: 2019-02-27 18:44:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/472#discussion_r260889253  

What's the reason behind such changes? Reducing use of standard library (compile time)?

> Username: Kojoley  
> Created_at: 2019-02-27 18:55:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/472#discussion_r260893416  

Removed surprising move of lvalue. And yes, `std::forward` is not zero cost in terms of compile time (also a surprising thing).


---
