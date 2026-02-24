# #114 Added definition for function checked_downcast_impl [Closed]

> Username: SPKorhonen  
> Created at: 2017-02-17 13:35:55 UTC  
> Updated at: 2017-06-08 19:54:16 UTC  
> Closed at: 2017-06-08 19:54:16 UTC  
> Url: https://github.com/boostorg/python/pull/114  

Header pyobject_type.hpp defines function checked_downcast_impl but it is not defined anywhere as far as I can see. It is used by pyobject_type::checked_downcast, which is further referenced by boost::numpy in header numpy_object_mgr_traits.hpp.  
  
Also see issue #113. Causes problems under Windows when using shared libraries

---

## Review 1 [Changes requested]

> Username: stefanseefeld  
> Created_at: 2017-03-20 17:06:44 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/python/pull/114#pullrequestreview-27904758  

Can you move the definition to the header file, i.e. simply replace the forward-declaration of the function by its definition ?

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2017-06-08 19:54:16 UTC  
> Url: https://github.com/boostorg/python/pull/114#issuecomment-307209619  

This is obsoleted by https://github.com/boostorg/python/commit/edd890bd2bdb89ac0d9fbcf5b6ba1dcc0bb66717.

---
