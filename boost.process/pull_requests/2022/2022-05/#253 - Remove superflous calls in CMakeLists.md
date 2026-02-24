# #253 Remove superflous calls in CMakeLists [Merged]

> Username: Flamefire  
> Created at: 2022-05-23 14:14:28 UTC  
> Updated at: 2022-06-02 20:28:03 UTC  
> Merged at: 2022-06-02 17:05:51 UTC  
> Closed at: 2022-06-02 17:05:52 UTC  
> Url: https://github.com/boostorg/process/pull/253  

The find_package is not required, as the dependencies are done using the superproject build  
The include_directories is already there: target_include_directories  
  
Partial revert of 610b337fa3bc82ecd19cd7baa6e90874f62c050f in #250

---

## Comment 1

> Username: Flamefire  
> Created_at: 2022-05-23 15:52:08 UTC  
> Url: https://github.com/boostorg/process/pull/253#issuecomment-1134848693  

@klemens-morgenstern Please merge soon as this blocks the Boost CMake build  
  
I suggest to add one for this lib (see boost-ci)

---
