# #90 removed c++03 polyfills [Merged]

> Username: joaquintides  
> Created at: 2025-11-07 12:19:42 UTC  
> Updated at: 2025-11-07 17:37:44 UTC  
> Merged at: 2025-11-07 17:37:41 UTC  
> Closed at: 2025-11-07 17:37:41 UTC  
> Url: https://github.com/boostorg/multi_index/pull/90  

Removed internal workarounds and fallbacks to support pre-C++11 compilers.

---

## Review 1 [Commented]

> Username: Flamefire  
> Created_at: 2025-11-07 13:27:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multi_index/pull/90#pullrequestreview-3433916522  

You should specify the C++11 requirement in CMake too, although it likely gets pulled in by dependencies anyway, but be explicit

> Username: Flamefire  
> Created_at: 2025-11-07 13:26:02 UTC  
> Updated_at: 2025-11-07 13:29:18 UTC  
> Url: https://github.com/boostorg/multi_index/pull/90#discussion_r2503532984  

```diff  
-cmake_minimum_required(VERSION 3.5...3.16)  
+cmake_minimum_required(VERSION 3.8...3.16)  
```

---

> Username: Flamefire  
> Created_at: 2025-11-07 13:26:44 UTC  
> Updated_at: 2025-11-07 13:29:29 UTC  
> Url: https://github.com/boostorg/multi_index/pull/90#discussion_r2503536252  

```diff  
+target_compile_features(boost_multi_index INTERFACE cxx_std_11)  
```


---
