# #227 Optimize `emplace()` for exactly a `value_type` or `init_type` argument [Merged]

> Username: k3DW  
> Created at: 2023-12-27 06:03:41 UTC  
> Updated at: 2023-12-31 17:56:15 UTC  
> Merged at: 2023-12-31 17:56:15 UTC  
> Closed at: 2023-12-31 17:56:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/227  

Closes #225

---

## Review 1 [Commented]

> Username: joaquintides  
> Created_at: 2023-12-27 12:10:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/227#pullrequestreview-1797128499  

📁 test/cfoa/emplace_tests.cpp

```diff
 219 |+       if (std::is_same<T, typename X::value_type>::value &&
 220 |+           !std::is_same<typename X::key_type,
 221 |+             typename X::value_type>::value) { // map value_type can only be
```

> Username: joaquintides  
> Created_at: 2023-12-27 12:10:22 UTC  
> Updated_at: 2023-12-27 12:10:23 UTC  
> Url: https://github.com/boostorg/unordered/pull/227#discussion_r1437002075  

This generates a C4127 warning in VS. See [this](https://github.com/boostorg/unordered/blob/0d2751c5e1a99b1275717e26c16eb672150da77b/include/boost/unordered/detail/foa/core.hpp#L1493-L1496) and [this](https://github.com/boostorg/unordered/blob/0d2751c5e1a99b1275717e26c16eb672150da77b/include/boost/unordered/detail/foa/core.hpp#L1556-L1558) for a workaround.

> Username: k3DW  
> Created_at: 2023-12-27 18:58:09 UTC  
> Url: https://github.com/boostorg/unordered/pull/227#discussion_r1437205582  

I tried to use `#pragma warning(suppress : 4127)` but that didn't seem to work. I'll go with the `push`, `disable`, `pop` method.


---

## Comment 2

> Username: k3DW  
> Created_at: 2023-12-27 21:34:58 UTC  
> Url: https://github.com/boostorg/unordered/pull/227#issuecomment-1870641290  

I made a mistake and forgot to add the new `emplace_smf_tests.cpp` to the Jamfile

---
