# #278 [appveyor] Add C++17 builds with VS2017 [Merged]

> Username: mloskot  
> Created at: 2019-04-11 08:50:27 UTC  
> Updated at: 2019-04-11 13:07:50 UTC  
> Merged at: 2019-04-11 13:07:45 UTC  
> Closed at: 2019-04-11 13:07:45 UTC  
> Url: https://github.com/boostorg/gil/pull/278  

For existing builds, bump C++ version to 14 which [lowest effective version (and default)](https://docs.microsoft.com/en-us/cpp/build/reference/std-specify-language-standard-version?view=vs-2017) supported by compiler from VS2017.  
  
### References  
  
* Since added C++17 builds use Boost `develop` and not older 1.68, if they pass, it means there indeed is MP11 issue in Boost 1.68 (see https://github.com/boostorg/gil/pull/274#issuecomment-482022037).  
  
### Tasklist  
  
- [x] All CI builds and checks have passed

---
