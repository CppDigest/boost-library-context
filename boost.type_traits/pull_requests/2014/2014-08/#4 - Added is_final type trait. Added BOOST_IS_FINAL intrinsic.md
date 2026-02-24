# #4 Added is_final type trait. Added BOOST_IS_FINAL intrinsic. [Merged]

> Username: K-ballo  
> Created at: 2014-08-11 12:46:51 UTC  
> Updated at: 2014-08-20 23:20:40 UTC  
> Merged at: 2014-08-20 17:15:48 UTC  
> Closed at: 2014-08-20 17:15:48 UTC  
> Url: https://github.com/boostorg/type_traits/pull/4  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-08-15 17:38:46 UTC  
> Url: https://github.com/boostorg/type_traits/pull/4#issuecomment-52335422  

I'm not against adding this trait, but:  
- The new files should have your copyright not mine ;-)  
- We should probably add a BOOST_NO_CXX11_FINAL macro to Boost.Config (can't believe we haven't already) and then use that as the condition in test.hpp.  This will of course cause some compilers (MSVC) to fail the tests, but that's sort of the point...

---

## Comment 2

> Username: K-ballo  
> Created_at: 2014-08-15 18:33:43 UTC  
> Url: https://github.com/boostorg/type_traits/pull/4#issuecomment-52341900  

Agreed. I'll look into adding a macro to Boost.Config. Hopefully a single one will do for all _virt-specifiers_.

---
