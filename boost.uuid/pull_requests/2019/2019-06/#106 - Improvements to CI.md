# #106 Improvements to CI [Merged]

> Username: jeking3  
> Created at: 2019-06-08 09:27:19 UTC  
> Updated at: 2019-06-08 10:33:33 UTC  
> Merged at: 2019-06-08 10:33:31 UTC  
> Closed at: 2019-06-08 10:33:31 UTC  
> Url: https://github.com/boostorg/uuid/pull/106  

This adds some new compilers and reduces the overall workload of the CI by cutting back the number of cxxstd and variant combinations in total.  
  
- Add Visual Studio 2019 on AppVeyor  
- Add gcc-9 on Travis  
- Reduce the cxxstd matrix to 2 choices on most Travis CI jobs  
- Build mostly release variant with a few debug variants sprinkled in  
- Disable cppcheck builds as they are hanging on some repositories

---
