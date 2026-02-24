# #59 Make zstd check agnostic to generator expressions and force CONFIG mode [Merged]

> Username: Neumann-A  
> Created at: 2024-04-19 21:44:25 UTC  
> Updated at: 2024-04-20 15:41:41 UTC  
> Merged at: 2024-04-20 14:16:15 UTC  
> Closed at: 2024-04-20 14:16:15 UTC  
> Url: https://github.com/boostorg/cmake/pull/59  

`zstd::libzstd_(shared|static)` targets have to be found via `CONFIG` mode  
  
`STREQUAL` is not agnositic to the targets being wrapped within a generator expression like:   
`$<IF:$<TARGET_EXISTS:zstd::libzstd_shared>,zstd::libzstd_shared,zstd::libzstd_static>`

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-04-19 23:40:11 UTC  
> Url: https://github.com/boostorg/cmake/pull/59#issuecomment-2067396140  

`find_dependency` with `CONFIG` requires CMake 3.9, although I'm not sure whether this will be a problem in this specific case.

---

## Comment 2

> Username: Neumann-A  
> Created_at: 2024-04-20 06:22:06 UTC  
> Url: https://github.com/boostorg/cmake/pull/59#issuecomment-2067574346  

> find_dependency with CONFIG requires CMake 3.9  
  
Where did you find that version restriction?  
  
https://github.com/boostorg/cmake/blob/3a5c500a490ec25c502b912bc0357249b0816fd0/include/BoostRoot.cmake#L279  
  
Claims boost requires at least 3.13

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-04-20 07:33:00 UTC  
> Url: https://github.com/boostorg/cmake/pull/59#issuecomment-2067590634  

> Where did you find that version restriction?  
  
In the documentation.  
  
https://cmake.org/cmake/help/v3.8/module/CMakeFindDependencyMacro.html  
https://cmake.org/cmake/help/v3.9/module/CMakeFindDependencyMacro.html  
  
> Claims boost requires at least 3.13  
  
For installation, yes. So it shouldn't be an issue here.

---
