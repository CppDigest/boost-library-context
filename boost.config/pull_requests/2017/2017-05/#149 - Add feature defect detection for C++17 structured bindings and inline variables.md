# #149 Add feature defect detection for C++17 structured bindings and inline variables [Merged]

> Username: glenfe  
> Created at: 2017-05-29 14:04:12 UTC  
> Updated at: 2017-05-29 17:32:37 UTC  
> Merged at: 2017-05-29 17:32:37 UTC  
> Closed at: 2017-05-29 17:32:37 UTC  
> Url: https://github.com/boostorg/config/pull/149  



---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2017-05-29 16:58:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/149#pullrequestreview-40806494  

📁 include/boost/config/compiler/visualc.hpp

```diff
 222 |+ #define BOOST_NO_CXX17_STRUCTURED_BINDINGS
 223 |+ #define BOOST_NO_CXX17_INLINE_VARIABLES
 224 |+ #define BOOST_NO_CXX17_FOLD_EXPRESSIONS
```

> Username: jzmaddock  
> Created_at: 2017-05-29 16:58:34 UTC  
> Url: https://github.com/boostorg/config/pull/149#discussion_r118966141  

Is there a reason for unconditionally defining these here, rather than using the std feature macros?  Just curious, John.

> Username: glenfe  
> Created_at: 2017-05-29 17:16:52 UTC  
> Url: https://github.com/boostorg/config/pull/149#discussion_r118967711  

I had heard that VC has no intention to support the SD-6 macros, but this information might be out of date. I can update the PR if you think it worth it (up until 2017 they are not supported, at least).

> Username: jzmaddock  
> Created_at: 2017-05-29 17:32:22 UTC  
> Url: https://github.com/boostorg/config/pull/149#discussion_r118969082  

Lets ignore it for now then ;)


---
