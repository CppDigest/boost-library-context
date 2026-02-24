# #12 clean compile; add missing declaration [Closed]

> Username: frederich  
> Created at: 2018-05-13 10:53:39 UTC  
> Updated at: 2018-09-01 18:38:24 UTC  
> Closed at: 2018-09-01 18:38:11 UTC  
> Url: https://github.com/boostorg/typeof/pull/12  

There is a compilation problem with MSVC 15.7.1 and /std:c++latest.  
  
    1>c:\libraries\boost_1_64_0\boost\typeof\msvc\typeof_impl.hpp(128): error C2988: unrecognizable template declaration/definition  
    1>c:\libraries\boost_1_64_0\boost\typeof\msvc\typeof_impl.hpp(136): note: see reference to class template instantiation 'boost::type_of::msvc_extract_type<ID,T>' being compiled  
    1>c:\libraries\boost_1_64_0\boost\typeof\msvc\typeof_impl.hpp(128): error C2143: syntax error: missing ';' before '<'  
    1>c:\libraries\boost_1_64_0\boost\typeof\msvc\typeof_impl.hpp(128): error C2913: explicit specialization; 'boost::type_of::id2type_impl' is not a specialization of a class template  
    1>c:\libraries\boost_1_64_0\boost\typeof\msvc\typeof_impl.hpp(128): error C2059: syntax error: '<'  
    1>c:\libraries\boost_1_64_0\boost\typeof\msvc\typeof_impl.hpp(129): error C2334: unexpected token(s) preceding '{'; skipping apparent function body  
  
The PR fix it.

---

## Comment 1

> Username: frederich  
> Created_at: 2018-09-01 18:38:24 UTC  
> Url: https://github.com/boostorg/typeof/pull/12#issuecomment-417879038  

sf

---
