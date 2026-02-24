# #67 Remove dependency on Boost.ForEach from CMakeLists.txt. [Merged]

> Username: Lastique  
> Created at: 2023-03-27 13:29:46 UTC  
> Updated at: 2023-03-27 16:33:11 UTC  
> Merged at: 2023-03-27 16:31:45 UTC  
> Closed at: 2023-03-27 16:31:45 UTC  
> Url: https://github.com/boostorg/multi_index/pull/67  

No public headers depend on Boost.ForEach headers, so the dependency in CMakeLists.txt is not needed.  
  
This dependency causes problems in downstream CI runs because boostdep does not find the dependency on Boost.ForEach from public headers and does not checkout Boost.ForEach git repo. This causes CMake errors because it cannot resolve the Boost::foreach target.

---

## Comment 1

> Username: Lastique  
> Created_at: 2023-03-27 15:04:33 UTC  
> Url: https://github.com/boostorg/multi_index/pull/67#issuecomment-1485286113  

The CI failure is not related to this change.

---
