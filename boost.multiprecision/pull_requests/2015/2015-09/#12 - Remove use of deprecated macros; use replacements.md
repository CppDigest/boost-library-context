# #12 Remove use of deprecated macros; use replacements [Merged]

> Username: mclow  
> Created at: 2015-09-10 22:45:57 UTC  
> Updated at: 2015-09-12 01:11:29 UTC  
> Merged at: 2015-09-11 07:59:57 UTC  
> Closed at: 2015-09-11 07:59:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/12  

Replace deprecated macro `BOOST_NO_CONSTEXPR` with `BOOST_NO_CXX11_CONSTEXPR` and `BOOST_NO_NOEXCEPT` with `BOOST_NO_CXX11_NOEXCEPT`.  
  
These were deprecated in the 1.50 and 1.51 releases, and I'd like to remove them in the 1.60 release.  
  
No functionality change.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-09-11 07:59:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/12#issuecomment-139478071  

Thanks!

---
