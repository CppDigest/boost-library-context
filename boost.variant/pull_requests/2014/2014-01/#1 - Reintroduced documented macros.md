# #1 Reintroduced documented macros [Closed]

> Username: K-ballo  
> Created at: 2014-01-17 16:35:16 UTC  
> Updated at: 2014-01-18 16:24:57 UTC  
> Closed at: 2014-01-18 16:24:57 UTC  
> Url: https://github.com/boostorg/variant/pull/1  

`BOOST_VARIANT_LIMIT_TYPES` and related macros are part of the `variant` documented interface, and should be defined even when variadic templates are used.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2014-01-18 09:00:21 UTC  
> Url: https://github.com/boostorg/variant/pull/1#issuecomment-32677684  

Patch is incorrect, it leads to redefinition of BOOST_VARIANT_ENUM_PARAMS and BOOST_VARIANT_ENUM_SHIFTED_PARAMS macros.  
  
Anyway, BOOST_VARIANT_LIMIT_TYPES and BOOST_VARIANT_RECURSIVE_VARIANT_MAX_ARITY are useless with variadic templates enabled. May be it would be better to notify user in docs, that those macro are not defined?

---

## Comment 2

> Username: K-ballo  
> Created_at: 2014-01-18 16:00:51 UTC  
> Url: https://github.com/boostorg/variant/pull/1#issuecomment-32685134  

I agree that at some point that functionality will be useless, but at this point it is documented and used by people out there (I found this bug by chasing a weird compilation error at another Boost library). I would keep them around as long as there is a non-variadic implementation of `variant`, since it's the easiest way for user code to support both variadic and non-variadic implementations. New code targeting only **C++11** compilers can ignore these macros completely.  
  
I'll fix the patch.

---

## Comment 3

> Username: K-ballo  
> Created_at: 2014-01-18 16:24:57 UTC  
> Url: https://github.com/boostorg/variant/pull/1#issuecomment-32685700  

The changes to `BOOST_VARIANT_ENUM_PARAMS` and `BOOST_VARIANT_ENUM_SHIFTED_PARAMS` are a breaking change that cannot be "fixed". I'm closing this pull request and raising the issue on the mailing list.

---
