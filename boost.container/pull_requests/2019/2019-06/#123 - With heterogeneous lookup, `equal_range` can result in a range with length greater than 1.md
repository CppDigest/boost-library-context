# #123 With heterogeneous lookup, `equal_range` can result in a range with length greater than 1. [Merged]

> Username: JanEisenhauer  
> Created at: 2019-06-07 09:57:33 UTC  
> Updated at: 2019-06-22 08:23:01 UTC  
> Merged at: 2019-06-22 08:23:01 UTC  
> Closed at: 2019-06-22 08:23:01 UTC  
> Url: https://github.com/boostorg/container/pull/123  

Do we need tests?

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2019-06-08 10:56:50 UTC  
> Url: https://github.com/boostorg/container/pull/123#issuecomment-500115236  

Can you please add a small test case that reproduces the reported error? Thanks

---

## Comment 2

> Username: JanEisenhauer  
> Created_at: 2019-06-11 10:17:49 UTC  
> Url: https://github.com/boostorg/container/pull/123#issuecomment-500779949  

Is it acceptable to have the `struct with_lookup_by_first` in both `flat_set_test.cpp`  and `flat_map_test.cpp`?

---

## Comment 3

> Username: igaztanaga  
> Created_at: 2019-06-22 08:21:37 UTC  
> Url: https://github.com/boostorg/container/pull/123#issuecomment-504643206  

Many thanks, it seems that "count()" has the same problem. I'll apply a fix on top of your patch.

---
