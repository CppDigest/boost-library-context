# #207 cmake: link system libraries [Merged]

> Username: timblechmann  
> Created at: 2023-12-17 04:12:55 UTC  
> Updated at: 2024-07-06 20:40:27 UTC  
> Merged at: 2024-07-06 20:40:21 UTC  
> Closed at: 2024-07-06 20:40:21 UTC  
> Url: https://github.com/boostorg/interprocess/pull/207  

fixes https://github.com/boostorg/lockfree/actions/runs/7236168354/job/19714404066

---

## Review 1 [Commented]

> Username: timblechmann  
> Created_at: 2023-12-17 04:13:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/interprocess/pull/207#pullrequestreview-1785335145  

📁 CMakeLists.txt

```diff
  31 |+     target_link_libraries(boost_interprocess INTERFACE rt)
  32 |+ elseif (CMAKE_SYSTEM_NAME STREQUAL "Windows")
  33 |+     target_link_libraries(boost_interprocess INTERFACE ole32 oleaut32 psapi advapi32)
```

> Username: timblechmann  
> Created_at: 2023-12-17 04:13:43 UTC  
> Url: https://github.com/boostorg/interprocess/pull/207#discussion_r1429009462  

copied from `test/Jamfile.v2`


---

## Comment 2

> Username: igaztanaga  
> Created_at: 2024-07-06 20:40:26 UTC  
> Url: https://github.com/boostorg/interprocess/pull/207#issuecomment-2211961791  

Many thanks!

---
