# #17 Workaround for Oracle C++ compiler [Merged]

> Username: jzmaddock  
> Created at: 2015-08-20 12:36:32 UTC  
> Updated at: 2015-09-12 17:26:11 UTC  
> Merged at: 2015-09-12 17:26:11 UTC  
> Closed at: 2015-09-12 17:26:11 UTC  
> Url: https://github.com/boostorg/variant/pull/17  

This change reduces the number of Variant test failures from 23 to 6 when building with the Oracle compiler, and at least gets simple usage working.  
  
I could also fix this by disabling conditional noexcept support in Config, but since it seems to be mostly working otherwise, that seems to be a shame.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2015-09-12 15:20:54 UTC  
> Url: https://github.com/boostorg/variant/pull/17#issuecomment-139783740  

Is this pull request still required? Could the `has_nothrow_constructor` be fixed to deal with Oracle, or this bug appears only when `BOOST_NOEXCEPT_IF(boost::has_nothrow_constructor<internal_T0>::value)` used?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2015-09-12 17:16:55 UTC  
> Url: https://github.com/boostorg/variant/pull/17#issuecomment-139798299  

I believe it's when used in this context that the compiler chokes - the type_traits tests are otherwise passing.

---
