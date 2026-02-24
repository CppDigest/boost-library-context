# #22 Generate test name ignoring file extension [Merged]

> Username: Flamefire  
> Created at: 2022-07-19 08:16:23 UTC  
> Updated at: 2022-07-25 13:21:55 UTC  
> Merged at: 2022-07-25 09:53:50 UTC  
> Closed at: 2022-07-25 09:53:50 UTC  
> Url: https://github.com/boostorg/cmake/pull/22  

The test names are overly verbose containing a `_cpp` suffix.  
Remove that.  
  
A `grep -r --include 'CMakeLists.txt' _cpp` in the boost root shows nothing which relies on that.

---
