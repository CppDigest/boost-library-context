# #16 Tests: Fix C1128 error on msvc-14.1+ [Merged]

> Username: Kojoley  
> Created at: 2019-03-31 17:14:04 UTC  
> Updated at: 2019-04-18 15:54:06 UTC  
> Merged at: 2019-04-18 15:52:43 UTC  
> Closed at: 2019-04-18 15:52:43 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/16  

traits_test fails on msvc-14.1+ in 64bit debug mode with C1128 error.  
  
The `/bigobj` option should be in every supported msvc version (vs2005+).  
  
Fixes #15

---
