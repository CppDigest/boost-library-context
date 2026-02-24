# #59 Fix auto-pointer registration in Boost Python 1.60. [Merged]

> Username: vslashg  
> Created at: 2016-03-01 17:34:38 UTC  
> Updated at: 2016-03-06 17:58:40 UTC  
> Merged at: 2016-03-06 17:58:40 UTC  
> Closed at: 2016-03-06 17:58:40 UTC  
> Url: https://github.com/boostorg/python/pull/59  

The conditional instantiation magic of maybe_register_pointer_to_python() assumes that use_value_holder and use_back_reference will be one of the boost::mpl::bool_ types, but this assumption is no longer true in Boost 1.60, where they can be standard library bool wrappers instead.  
  
Explicitly defining these types as mpl::bool_ classes fixes https://github.com/boostorg/python/issues/56.

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2016-03-06 17:58:33 UTC  
> Url: https://github.com/boostorg/python/pull/59#issuecomment-192947949  

Thanks for the patch. I verified that this works locally, so let's merge this.

---
