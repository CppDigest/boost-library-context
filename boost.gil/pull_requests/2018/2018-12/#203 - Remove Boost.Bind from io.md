# #203 Remove Boost.Bind from io [Merged]

> Username: mloskot  
> Created at: 2018-12-18 01:40:18 UTC  
> Updated at: 2018-12-18 17:51:03 UTC  
> Merged at: 2018-12-18 17:50:58 UTC  
> Closed at: 2018-12-18 17:50:58 UTC  
> Url: https://github.com/boostorg/gil/pull/203  

Replace Boost.MPL boolean constants with C++11 equivalents.  
Replace `throw()` with `noexcept`.  
Replace empty constructor body with `default`.  
Rename private class members to avoid leading underscore (too easy to confuse as reserved identifier).  
Tidy up with compact formatting.  
  
### References  
  
- #202  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---
