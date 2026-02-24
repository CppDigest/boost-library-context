# #33 Fix MSVC compilation errors [Closed]

> Username: MarcelRaad  
> Created at: 2015-11-01 15:43:17 UTC  
> Updated at: 2017-09-19 07:56:49 UTC  
> Closed at: 2015-11-12 15:07:25 UTC  
> Url: https://github.com/boostorg/serialization/pull/33  

BOOST_SERIALIZATION_SOURCE has to be defined before the first  
serialization/config.hpp include. As singleton.hpp also includes config.hpp  
now, BOOST_SERIALIZATION_DECL was defined as BOOST_SYMBOL_IMPORT instead of  
BOOST_SYMBOL_EXPORT in the serialization source files, leading to C2491  
"definition of dllimport function not allowed" errors on MSVC when building  
serialization as a shared library.

---

## Comment 1

> Username: MarcelRaad  
> Created_at: 2015-11-12 15:07:25 UTC  
> Url: https://github.com/boostorg/serialization/pull/33#issuecomment-156132732  

Obsolete.

---
