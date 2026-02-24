# #120 Feature/appveyor msvc improvements [Merged]

> Username: jefftrull  
> Created at: 2020-10-28 23:48:57 UTC  
> Updated at: 2020-10-29 05:22:17 UTC  
> Merged at: 2020-10-29 05:22:17 UTC  
> Closed at: 2020-10-29 05:22:17 UTC  
> Url: https://github.com/boostorg/wave/pull/120  

Two changes:  
  
1) work around an issue with 32b builds on MSVC  
2) add VC++ 2019 to matrix  
  
both using code from @pdimov . Test in progress on appveyor; I'll try to get this in for 1.75 if it passes.

---
