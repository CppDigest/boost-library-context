# #146 Improve diagnostic for attempt to use `get_name` with non-external [Merged]

> Username: denzor200  
> Created at: 2023-11-25 18:14:45 UTC  
> Updated at: 2023-11-26 08:52:18 UTC  
> Merged at: 2023-11-26 08:52:18 UTC  
> Closed at: 2023-11-26 08:52:18 UTC  
> Url: https://github.com/boostorg/pfr/pull/146  



---

## Review 1 [Commented]

> Username: denzor200  
> Created_at: 2023-11-25 19:09:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/146#pullrequestreview-1749228210  

📁 include/boost/pfr/detail/core_name20_static.hpp

```diff
 169 |                 core_name_skip, detail::make_clang_wrapper(std::addressof(
 170 |-                     fake_object<core_name_skip>.size_at_begin
 170 |+                     fake_object<core_name_skip>().size_at_begin
```

> Username: denzor200  
> Created_at: 2023-11-25 19:09:03 UTC  
> Updated_at: 2023-11-25 19:09:12 UTC  
> Url: https://github.com/boostorg/pfr/pull/146#discussion_r1405204756  

`detail::fake_object`, since it became a call


---

## Comment 2

> Username: denzor200  
> Created_at: 2023-11-25 21:28:59 UTC  
> Url: https://github.com/boostorg/pfr/pull/146#issuecomment-1826423599  

Something went wrong with clang on CI. Saw the same error in develop branch from my fork

---
