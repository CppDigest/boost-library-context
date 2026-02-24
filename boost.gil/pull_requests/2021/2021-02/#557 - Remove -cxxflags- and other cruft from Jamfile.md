# #557 Remove <cxxflags> and other cruft from Jamfile [Merged]

> Username: mloskot  
> Created at: 2021-02-02 21:48:01 UTC  
> Updated at: 2021-02-02 23:47:21 UTC  
> Merged at: 2021-02-02 23:47:17 UTC  
> Closed at: 2021-02-02 23:47:17 UTC  
> Url: https://github.com/boostorg/gil/pull/557  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
The warnings, debug-symbols and other build properties  
can (and should) be controlled via b2 command line options.  
  
By the way, most of `<cxxflags>` uses in `Jamfile`-s [are usually incorrect](https://cpplang.slack.com/archives/C27KZLB0X/p1611538905042200).  
Instead, [better option is to](https://cpplang.slack.com/archives/C27KZLB0X/p1611592269057100?thread_ts=1611553747.045400&cid=C27KZLB0X):  
  
> use `warnings=pedantic` to control the warnings instead of `cxxflags` options directly  
> either in `requirements` or in `default-build`, depending on whether you want to override it from the command line or not  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass

---
