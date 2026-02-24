# #30 Update PGI C++ compiler support [Merged]

> Username: dkolsen-pgi  
> Created at: 2017-07-26 19:50:05 UTC  
> Updated at: 2017-07-26 23:49:26 UTC  
> Merged at: 2017-07-26 20:03:23 UTC  
> Closed at: 2017-07-26 20:03:23 UTC  
> Url: https://github.com/boostorg/mpl/pull/30  

Add PGI C++ to list of compilers that use enums rather than static constants for compile-time assertions, because, like GCC, PGI issues warnings for unused static constants.

---

## Comment 1

> Username: mclow  
> Created_at: 2017-07-26 20:03:25 UTC  
> Url: https://github.com/boostorg/mpl/pull/30#issuecomment-318166293  

Do we have a test bot for this compiler?

---

## Comment 2

> Username: dkolsen-pgi  
> Created_at: 2017-07-26 20:07:48 UTC  
> Url: https://github.com/boostorg/mpl/pull/30#issuecomment-318167407  

No, there is no test bot for the PGI compiler.  Setting one up is a future project of mine, after I get all the build issues fixed.

---

## Comment 3

> Username: Lastique  
> Created_at: 2017-07-26 21:47:37 UTC  
> Url: https://github.com/boostorg/mpl/pull/30#issuecomment-318192568  

Is this workaround needed because the compiler doesn't support static constants? If so, why not do the workaround in `BOOST_STATIC_CONSTANT` instead? The macro is used throughout Boost, surely the same issue will appear elsewhere.

---

## Comment 4

> Username: dkolsen-pgi  
> Created_at: 2017-07-26 23:49:26 UTC  
> Url: https://github.com/boostorg/mpl/pull/30#issuecomment-318215740  

PGI supports static constants just fine, so I don't want to change the definition of BOOST_STATIC_CONSTANT.  But PGI outputs a warning when a static constant is defined within a function but never used, which is exactly what MPL does for its compile-time assertions.  To avoid a warning whenever BOOST_MPL_ASSERT and friends appear at function scope, the definition of BOOST_MPL_ASSERT needs to be changed, not the definition of BOOST_STATIC_CONSTANT.

---
