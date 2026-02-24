# #301 - Unreachable code warning using static_vector with visual studio 17.12 with W4 in release build [Closed]

> Username: rconde01  
> Created at: 2025-06-03 19:25:47 UTC  
> Updated at: 2025-10-26 22:08:49 UTC  
> Closed at: 2025-10-26 22:08:48 UTC  
> Url: https://github.com/boostorg/container/issues/301  

This warning happens here: boost/container/vector.hpp line 140  
```  
   //Constructors  
   inline vec_iterator() BOOST_NOEXCEPT_OR_NOTHROW  
      : m_ptr()   //Value initialization to achieve "null iterators" (N3644) <--  
   {}  
```  
Sorry - I would make a repro on compiler explorer but it doesn't work with libraries and MSVC.

---

## Comment 1

> Username: rconde01  
> Created at: 2025-06-04 14:54:58 UTC  
> Url: https://github.com/boostorg/container/issues/301#issuecomment-2940360236  

Got a minimal reproduction:  
```  
#include <boost/container/static_vector.hpp>  
  
__declspec(dllexport) auto func() -> int {  
   boost::container::static_vector<int, 10> vvv{};  
  
   vvv.push_back(1);  
  
   return vvv.back();  
}  
```

---

## Comment 2

> Username: igaztanaga  
> Created at: 2025-10-26 22:08:49 UTC  
> Url: https://github.com/boostorg/container/issues/301#issuecomment-3448960747  

The problem can't be reproduced with current develop commit (https://github.com/boostorg/container/commit/e18078f846a32a346e34b5bfae409befb5c359c7), neither in x86 nor x64 builds
