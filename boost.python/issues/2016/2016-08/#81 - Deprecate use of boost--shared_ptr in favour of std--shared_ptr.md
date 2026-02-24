# #81 - Deprecate use of boost::shared_ptr in favour of std::shared_ptr [Open]

> Username: stefanseefeld  
> Created at: 2016-08-05 14:11:02 UTC  
> Updated at: 2016-09-27 18:42:25 UTC  
> Url: https://github.com/boostorg/python/issues/81  

Assuming `boost::shared_ptr` and `std::shared_ptr` are distinct types, let Boost.Python provide builtin support for `std::shared_ptr` whenever the compiler supports it, and optionally support for `boost::shared_ptr` (for backward compatibility). The latter may be removed later, to reduce Boost.Python's dependency on Boost.

---

## Comment 1

> Username: eudoxos  
> Created at: 2016-09-10 14:04:10 UTC  
> Url: https://github.com/boostorg/python/issues/81#issuecomment-246113473  

This depends on https://github.com/boostorg/python/issues/29, i.e. making std::shared_ptr support complete (in particular, implicit upcasing, which works for boost::shared_ptr but not std::shared_ptr).

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2016-09-27 18:42:25 UTC  
> Url: https://github.com/boostorg/python/issues/81#issuecomment-249958415  

Support for `std::shared_ptr` has been added as per https://github.com/boostorg/python/commit/97e4b34a15978ca9d7c296da2de89b78bba4e0d5.  
This still needs documentation.
