# #298 Add SFINAE-friendly version of std::common_type [Merged]

> Username: mloskot  
> Created at: 2019-04-30 14:33:04 UTC  
> Updated at: 2019-05-02 12:40:35 UTC  
> Merged at: 2019-05-02 12:38:22 UTC  
> Closed at: 2019-05-02 12:38:22 UTC  
> Url: https://github.com/boostorg/gil/pull/298  

It should help to improve uses of SFINAE like the one from #295 and fix #289 for MSVC 14.0 (VS2015) for good.  
  
Copied from Boost.Hana.  
  
### Rationale  
  
Simply, SFINAE using `std::common_type` turned out to be insufficient, at least it was still not working well with VS2015. The issue was revealed by `matrix3x2` tests added in #293 for which VS2015 build jobs failed (e.g. https://ci.appveyor.com/project/mloskot/gil/build/job/c7xrfsikib6la83g).  
  
Since the `std::common_type` is a very useful trait and it may be suitable in future cases where common type needs to be calculated, it is a good idea to have a solid SFINAE-friendly version available.  
  
### References  
  
* Refines #295 which was based on the idea from https://github.com/boostorg/gil/pull/294#issuecomment-486805343  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve  
  
------

---
