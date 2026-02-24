# #152 Make utf8_codecvt::out return partial for trailing surrogates [Merged]

> Username: Flamefire  
> Created at: 2023-03-05 17:17:53 UTC  
> Updated at: 2023-03-06 15:37:10 UTC  
> Merged at: 2023-03-06 01:58:43 UTC  
> Closed at: 2023-03-06 01:58:43 UTC  
> Url: https://github.com/boostorg/locale/pull/152  

When a high surrogate is encountered there must be something following.  
If it is not, the input is incomplete, i.e. "partial".  
  
See also https://github.com/boostorg/nowide/commit/14675cd82290fae97da7bfb1cd3aa001679c6850  
  
Fixes #50

---
