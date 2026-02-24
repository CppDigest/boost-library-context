# #120 Specify LANGUAGES NONE to avoid enabling C and CXX [Merged]

> Username: pdimov  
> Created at: 2021-09-18 16:40:44 UTC  
> Updated at: 2021-10-23 17:15:03 UTC  
> Merged at: 2021-10-23 17:15:03 UTC  
> Closed at: 2021-10-23 17:15:03 UTC  
> Url: https://github.com/boostorg/predef/pull/120  

If `LANGUAGES` is not given, it defaults to `C CXX`. This results in Predef unnecessarily configuring the C and C++ compilers.

---
