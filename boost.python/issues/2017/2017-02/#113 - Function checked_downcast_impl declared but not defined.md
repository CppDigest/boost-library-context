# #113 - Function checked_downcast_impl declared but not defined [Closed]

> Username: SPKorhonen  
> Created at: 2017-02-17 10:03:57 UTC  
> Updated at: 2017-06-08 19:54:44 UTC  
> Closed at: 2017-06-08 19:54:44 UTC  
> Url: https://github.com/boostorg/python/issues/113  

Header pyobject_type.hpp defines function checked_downcast_impl but it is not defined anywhere as far as I can see. It is used by pyobject_type::checked_downcast, which is further referenced by boost::numpy in header numpy_object_mgr_traits.hpp.  
  
Function checked_downcast_impl should be defined or removed.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-06-08 19:54:44 UTC  
> Url: https://github.com/boostorg/python/issues/113#issuecomment-307209739  

This was done in https://github.com/boostorg/python/commit/edd890bd2bdb89ac0d9fbcf5b6ba1dcc0bb66717.
