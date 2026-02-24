# #77 Use static const for next/prior from C++11 [Merged]

> Username: ecatmur  
> Created at: 2023-11-24 15:53:45 UTC  
> Updated at: 2024-06-18 00:12:57 UTC  
> Merged at: 2024-06-18 00:12:57 UTC  
> Closed at: 2024-06-18 00:12:57 UTC  
> Url: https://github.com/boostorg/mpl/pull/77  

Trying to form next/prior in constant evaluation may be ill-formed. Fixes #69.

---

## Review 1 [Commented]

> Username: carlosgalvezp  
> Created_at: 2023-11-27 09:02:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mpl/pull/77#pullrequestreview-1749940482  

📁 include/boost/mpl/aux_/integral_wrapper.hpp

```diff
  59 |- #if BOOST_WORKAROUND(__EDG_VERSION__, <= 243)
  59 |+ #if BOOST_WORKAROUND(__EDG_VERSION__, <= 243) \
  60 |+     || __cplusplus >= 201703L
```

> Username: carlosgalvezp  
> Created_at: 2023-11-27 09:02:24 UTC  
> Url: https://github.com/boostorg/mpl/pull/77#discussion_r1405840109  

I believe the warning is active from C++11 and onwards.

> Username: ecatmur  
> Created_at: 2023-11-27 15:55:42 UTC  
> Updated_at: 2023-11-27 15:55:43 UTC  
> Url: https://github.com/boostorg/mpl/pull/77#discussion_r1406381259  

Changed, thanks. I was following someone's comment here https://github.com/llvm/llvm-project/issues/50055#issue-1077380777 ;)

> Username: carlosgalvezp  
> Created_at: 2023-11-27 16:02:59 UTC  
> Updated_at: 2023-11-27 16:03:00 UTC  
> Url: https://github.com/boostorg/mpl/pull/77#discussion_r1406392830  

Yep, technically this has moved from "unspecified behavior" in <=C++14 to "undefined behavior" in C++17 onwards. But the LLVM implementation warns starting on C++11. Not sure what the correct behavior would be (is there any?), but that's how it works today :)   
  
https://github.com/llvm/llvm-project/pull/67528/files#diff-24549b124046a492279fe53ffe2aca86550f281b025a48067c043a1c944547faR3


---
