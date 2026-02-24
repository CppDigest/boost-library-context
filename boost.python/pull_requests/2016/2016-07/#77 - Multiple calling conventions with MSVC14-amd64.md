# #77 Multiple calling conventions with MSVC14/amd64 [Merged]

> Username: cowo78  
> Created at: 2016-07-21 08:37:07 UTC  
> Updated at: 2016-07-21 12:17:18 UTC  
> Merged at: 2016-07-21 11:59:14 UTC  
> Closed at: 2016-07-21 11:59:14 UTC  
> Url: https://github.com/boostorg/python/pull/77  

Avoid multiple template instances of boost::python::details::get_signature when multiple calling conventions are enabled

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2016-07-21 11:59:08 UTC  
> Url: https://github.com/boostorg/python/pull/77#issuecomment-234233118  

This looks good. Thanks for working on it !

---

## Comment 2

> Username: cowo78  
> Created_at: 2016-07-21 12:17:18 UTC  
> Url: https://github.com/boostorg/python/pull/77#issuecomment-234236560  

The added ifdef should be fairly general, but I have no idea about why the 32bit target compiler did not complain about multiple definitions.

---
