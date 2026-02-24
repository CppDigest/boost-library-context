# #48 Performance Improvement [Merged]

> Username: BrianWeed  
> Created at: 2024-02-04 19:36:18 UTC  
> Updated at: 2024-02-06 07:08:46 UTC  
> Merged at: 2024-02-06 07:08:46 UTC  
> Closed at: 2024-02-06 07:08:46 UTC  
> Url: https://github.com/boostorg/function/pull/48  

Move functors where possible to reduce the number of copies (Lambdas with expensive-to-copy captures can be a bottleneck when copied)

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2024-02-04 19:44:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/function/pull/48#pullrequestreview-1861547519  

📁 include/boost/function/function_template.hpp

```diff
 458 |             // on giving cv-qualifiers to free functions
 459 |-             functor.members.func_ptr = reinterpret_cast<void (*)()>(f);
 459 |+               functor.members.func_ptr = reinterpret_cast<void (*)()>(f);
```

> Username: pdimov  
> Created_at: 2024-02-04 19:44:52 UTC  
> Url: https://github.com/boostorg/function/pull/48#discussion_r1477427972  

This whitespace change seems accidental.

> Username: BrianWeed  
> Created_at: 2024-02-04 20:06:06 UTC  
> Url: https://github.com/boostorg/function/pull/48#discussion_r1477430734  

Yes, that's a mistake


---
