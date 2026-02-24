# #233 Add C++14 builds using GCC 8 and VS2017 to Azure Pipelines [Merged]

> Username: mloskot  
> Created at: 2019-02-05 17:47:41 UTC  
> Updated at: 2019-02-06 13:57:45 UTC  
> Merged at: 2019-02-05 19:18:33 UTC  
> Closed at: 2019-02-05 19:18:34 UTC  
> Url: https://github.com/boostorg/gil/pull/233  

We need build jobs compiling GIL in C++14 mode to start verifying #231 and similar enhancements specific to C++ version.  
  
Tidy up build jobs naming to include C++ version.  
  
### Tasklist  
  
- [x] Azure Pipelines builds and checks have passed  
  
-----  
  
Requested by @sdebionne in https://github.com/boostorg/gil/pull/227#issuecomment-460552577 while prototyping the variant improvements finally submitted in #231

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-02-05 19:17:43 UTC  
> Url: https://github.com/boostorg/gil/pull/233#issuecomment-460767221  

AzP builds are fine https://dev.azure.com/boostorg/gil/_build/results?buildId=152

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-02-05 19:20:23 UTC  
> Url: https://github.com/boostorg/gil/pull/233#issuecomment-460768164  

@sdebionne Two C++14 builds are now in place on Azure Pipelines, see `_cxx14_` tag in job name.

---

## Comment 3

> Username: sdebionne  
> Created_at: 2019-02-06 13:57:44 UTC  
> Url: https://github.com/boostorg/gil/pull/233#issuecomment-461031869  

Thanks Mateusz!

---
