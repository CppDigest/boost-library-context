# #397 MSVC update that handled deprecation of <codecvt> and availability of… [Merged]

> Username: jzmaddock  
> Created at: 2021-08-17 16:33:26 UTC  
> Updated at: 2022-11-20 17:45:36 UTC  
> Merged at: 2021-08-19 15:37:51 UTC  
> Closed at: 2021-08-19 15:37:51 UTC  
> Url: https://github.com/boostorg/config/pull/397  

… <stop_token>.  
  
See also https://github.com/boostorg/config/issues/391.

---

## Review 1 [Commented]

> Username: Xerus2  
> Created_at: 2021-08-20 00:52:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/397#pullrequestreview-734580224  

📁 include/boost/config/stdlib/dinkumware.hpp

```diff
 239 |+ #endif
 240 |+ 
 241 |+ #if (_MSVC_LANG > 201700) && !defined(BOOST_NO_CXX11_HDR_CODECVT)
```

> Username: Xerus2  
> Created_at: 2021-08-20 00:52:09 UTC  
> Url: https://github.com/boostorg/config/pull/397#discussion_r692582942  

201703 is the correct _MSVC_LANG number to check here, not 201700. I'd write this check logic as _MSVC_LANG >= 201703.


---
