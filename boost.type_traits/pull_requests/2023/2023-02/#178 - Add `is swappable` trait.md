# #178 Add `is swappable` trait. [Merged]

> Username: Lastique  
> Created at: 2023-02-18 17:12:13 UTC  
> Updated at: 2023-02-23 21:20:16 UTC  
> Merged at: 2023-02-23 18:20:08 UTC  
> Closed at: 2023-02-23 18:20:09 UTC  
> Url: https://github.com/boostorg/type_traits/pull/178  

This PR adds `is_swappable` trait and makes `is_nothrow_swappable` reuse it to hopefully fix ICEs with gcc 4.6.  
  
Also, `is_(nothrow_)swappable` implementation namespace is now not within `boost` namespace to avoid any `boost::swap` overloads influencing the trait result, unless those overloads are found via ADL. Added tests to verify that ADL for swap overloads works.

---

## Comment 1

> Username: Lastique  
> Created_at: 2023-02-19 15:16:26 UTC  
> Url: https://github.com/boostorg/type_traits/pull/178#issuecomment-1436015078  

This is ready for review now.  
  
BTW, note that MSVC-14.0 does not support C++17, so the "windows_msvc_14_0 (17)" CI job is redundant (you can see the compiler is complaining about unsupported command line argument in the CI logs).

---

## Comment 2

> Username: Lastique  
> Created_at: 2023-02-23 17:26:46 UTC  
> Url: https://github.com/boostorg/type_traits/pull/178#issuecomment-1442161548  

@jzmaddock It would be nice to have this in Boost 1.82. Could you have a look, please?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2023-02-23 18:20:03 UTC  
> Url: https://github.com/boostorg/type_traits/pull/178#issuecomment-1442228284  

Thanks @Lastique , looks good to me.  I'll fix the CI issue separately.

---

## Comment 4

> Username: Lastique  
> Created_at: 2023-02-23 21:20:15 UTC  
> Url: https://github.com/boostorg/type_traits/pull/178#issuecomment-1442447333  

Thanks. Don't forget to merge to master.

---
