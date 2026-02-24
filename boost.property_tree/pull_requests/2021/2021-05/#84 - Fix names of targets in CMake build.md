# #84 Fix names of targets in CMake build: [Merged]

> Username: madmongo1  
> Created at: 2021-05-28 08:56:08 UTC  
> Updated at: 2021-05-28 14:42:34 UTC  
> Merged at: 2021-05-28 14:42:28 UTC  
> Closed at: 2021-05-28 14:42:28 UTC  
> Url: https://github.com/boostorg/property_tree/pull/84  

This project's CMakeLists.txt must exist in the boost ecosystem. In  
order to avoid ambiguity of target names across boost libraries, targets  
in this project are prefixed with boost_property_tree-

---
