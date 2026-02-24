# #196 - Using functionality in wchar_from_mb.hpp [Open]

> Username: eldiener  
> Created at: 2020-04-13 21:24:28 UTC  
> Updated at: 2020-04-21 03:37:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/196  

In wchar_from_mb.hpp you have at the beginning of the file:  
  
```  
#ifndef BOOST_NO_CWCHAR  
#include <cwchar>  // mbstate_t  
#endif  
```  
  
but you proceed in the functionality in the file as if BOOST_NO_CWCHAR can never be defined by referencing functionality in cwchar. If the functionality in wchar_from_mb.hpp can not work if BOOST_NO_CWCHAR is defined should not the serialization library adjust to this somehow, maybe by not supporting wide character archives at all in such a situation ?  
  
I am working with the Embarcadero C++ clang-based compilers trying to integrate them into Boost libraries. Unforunately those compilers have a bug, which I have reported to Embarcadero, that large parts of the functionality of cwchar are not provided despite the compilers supporting wchar_t. So in the embarcadero.hpp for these clang-based compilers I have BOOST_NO_CWCHAR defined. But this causes a compiler error in wchar_from_mb.hpp when you try to use functionality from the cwchar header file, such as std::mbsinit on line 133.

---

## Comment 1

> Username: robertramey  
> Created at: 2020-04-20 21:41:45 UTC  
> Url: https://github.com/boostorg/serialization/issues/196#issuecomment-616825734  

hmmm -   
  
When I search the code base I find 2 places where wchar_from_mb is called:  
a) xml_woarchive_impl.ipp  which is only included if the systems supports wide characters.  
b) test_iterators.cpp which has the #include <... wchar_from_mb> commented out und BOOST_NO_CWCHAR is not defined.  
  
So the way I see it, this problem will never occur.  It's possible that pithing wchar_from_mb.hpp could eliminate that #ifndef BOOST_NO_CWCHAR AS it seems redundant and confusing, but it's not an urgent error.

---

## Comment 2

> Username: eldiener  
> Created at: 2020-04-21 00:32:06 UTC  
> Url: https://github.com/boostorg/serialization/issues/196#issuecomment-616880352  

The compiler supports wide characters but has such a broken cwchar ( aka wchar.h ) that I do not turn off wide character support in Boost.Config but I do define BOOST_NO_CWCHAR..

---

## Comment 3

> Username: robertramey  
> Created at: 2020-04-21 02:04:19 UTC  
> Url: https://github.com/boostorg/serialization/issues/196#issuecomment-616906046  

Right - so from the point of view of the serialization library its not available.  My point above is that even though it's specified in the wchar_from_mb.hpp, the file is never included if BOOST_NO_CWCHAR is defined.   So the #if  BOOST_NO_CWCHAR ... actually redundant. It's misleading - but redundant.

---

## Comment 4

> Username: eldiener  
> Created at: 2020-04-21 03:37:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/196#issuecomment-616932466  

Later in wchar_from_mb.hpp you have:  
  
`BOOST_ASSERT(std::mbsinit(&m_mbs)); `  
  
but if BOOST_NO_CWCHAR is defined and cwchar is not included then this will lead to a compiler error, as indeed it does for Embarcadero.  
  
My general point is that if the serialization library can not do wide character serialization if BOOST_NO_CWCHAR is defined, which is perfectly understandable if this your design, then none of the functionality which does wide characater serialization in your library should ever be called in your code in that case.   
  
I have already reported the bug to Embarcadero that while they support wide characters for their clang-based compilers their functionality in cwchar ( wchar.h ) is incomplete and missing 32 functions, of which mbsinit is just one ( https://quality.embarcadero.com/browse/RSP-27977 ). Because of this I defined BOOST_NO_WCHAR for their clang-based compilers in the appropriate Boost.Config file.
