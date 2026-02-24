# #315 - Reverse include logic in throw_exception.hpp [Open]

> Username: gaspardpetit  
> Created at: 2024-06-08 01:17:09 UTC  
> Updated at: 2025-10-22 11:10:36 UTC  
> Url: https://github.com/boostorg/serialization/issues/315  

I believe commit b4412f2867450aaecee1e91bf287ab34ebaf9168 by @jzmaddock introduced a bug in the include logic - the `ifndef` should have been changed for an `ifdef`. The fact that it has been there for 2 years without anyone complaining makes me doubt, so let me know what you think :D  
  
See my note here: https://github.com/boostorg/serialization/commit/b4412f2867450aaecee1e91bf287ab34ebaf9168#r142878767  
  
Essentially the code looks like this:  
  
```  
#ifndef BOOST_NO_EXCEPTIONS  
#include <boost/throw_exception.hpp>  
#endif  
  
namespace boost {  
namespace serialization {  
  
#ifdef BOOST_NO_EXCEPTIONS  
  
BOOST_NORETURN inline void throw_exception(std::exception const & e) {  
    ::boost::throw_exception(e);  
}  
  
#else  
  
template<class E> BOOST_NORETURN inline void throw_exception(E const & e){  
    throw e;  
}  
  
#endif  
  
} // namespace serialization  
} // namespace boost  
```  
  
We need the include when we are going to call `::boost::throw_exception(e);` which gets called only when `defined(BOOST_NO_EXCEPTIONS)` is `true`. Therefore, it seems like the include should be on an `ifdef` rather than an `ifndef`.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-06-08 08:26:43 UTC  
> Url: https://github.com/boostorg/serialization/issues/315#issuecomment-2155872079  

I think you're correct - my bad - the error has probably been hidden because almost everything in boost includes boost/throw_exception.hpp anyway!

---

## Comment 2

> Username: paulmiller  
> Created at: 2025-03-21 02:36:29 UTC  
> Url: https://github.com/boostorg/serialization/issues/315#issuecomment-2742104118  

I just ran into this, and came up with the same fix.

---

## Comment 3

> Username: dokempf  
> Created at: 2025-10-22 11:10:36 UTC  
> Url: https://github.com/boostorg/serialization/issues/315#issuecomment-3431848436  

Same here. Found the issue in a code dive and came here to report it. Can we please merge https://github.com/boostorg/serialization/pull/316?
