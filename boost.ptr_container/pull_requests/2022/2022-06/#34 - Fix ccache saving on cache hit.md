# #34 Fix ccache saving on cache hit [Merged]

> Username: Flamefire  
> Created at: 2022-06-05 08:12:42 UTC  
> Updated at: 2023-02-24 15:16:35 UTC  
> Merged at: 2023-02-23 15:31:07 UTC  
> Closed at: 2023-02-23 15:31:07 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/34  

See boostorg/boost-ci#166

---

## Comment 1

> Username: Flamefire  
> Created_at: 2022-06-05 18:32:55 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/34#issuecomment-1146862160  

The serialization test seems to be broken, looks like the order is wrong:  
  
```  
libs/ptr_container/test/serialization.cpp(206): error: in "Pointer Container Test Suite/test_serialization": check (*vec2.begin()).i == -1 has failed [0 != -1]  
libs/ptr_container/test/serialization.cpp(207): error: in "Pointer Container Test Suite/test_serialization": check (*++vec2.begin()).i == 0 has failed [-1 != 0]  
libs/ptr_container/test/serialization.cpp(206): error: in "Pointer Container Test Suite/test_serialization": check (*vec2.begin()).i == -1 has failed [0 != -1]  
libs/ptr_container/test/serialization.cpp(207): error: in "Pointer Container Test Suite/test_serialization": check (*++vec2.begin()).i == 0 has failed [-1 != 0]  
```  
  
Likely by a change in Boost.Serialization, as the code hasn't been touched. Maybe @robertramey can help or @jeking3 could disable that test if the test itself is broken/invalid.

---
