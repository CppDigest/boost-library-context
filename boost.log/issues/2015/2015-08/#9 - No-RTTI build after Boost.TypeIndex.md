# #9 - No-RTTI build after Boost.TypeIndex? [Closed]

> Username: jpetso  
> Created at: 2015-08-14 00:24:53 UTC  
> Updated at: 2015-10-14 17:35:43 UTC  
> Closed at: 2015-08-14 00:59:56 UTC  
> Url: https://github.com/boostorg/log/issues/9  

I was wondering if, after switching to Boost.TypeIndex, it would be possible to enable builds without RTTI as per http://www.boost.org/doc/libs/master/doc/html/boost_typeindex/getting_started.html. The [build check in config.hpp](https://github.com/boostorg/log/blob/master/include/boost/log/detail/config.hpp#L30) is still there, is it feasible to make it build without RTTI and what kind of work would that involve?

---

## Comment 1

> Username: Lastique  
> Created at: 2015-08-14 00:59:56 UTC  
> Url: https://github.com/boostorg/log/issues/9#issuecomment-130906071  

Basically, I didn't have the time to test if it works with type info emulation. The check is still there because of that and because I will probably have to reflect that configuration option in the library namespace, if I'm going to offer it.  
  
If you want to try it out you can remove the check and build Boost with RTTI off or configure Boost.TypeIndex to use type info emulation (define BOOST_TYPE_INDEX_FORCE_NO_RTTI_COMPATIBILITY). You will have to use the same configuration when building your code as well. Let me know if you find any problems.

---

## Comment 2

> Username: Lastique  
> Created at: 2015-10-14 17:35:43 UTC  
> Url: https://github.com/boostorg/log/issues/9#issuecomment-148128508  

After examining the code more closely, RTTI is still required because the implementation uses `dynamic_cast` in a few places.
