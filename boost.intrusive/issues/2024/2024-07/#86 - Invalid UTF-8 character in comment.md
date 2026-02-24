# #86 - Invalid UTF-8 character in comment [Closed]

> Username: francoisk  
> Created at: 2024-07-18 08:56:04 UTC  
> Updated at: 2024-07-18 10:52:48 UTC  
> Closed at: 2024-07-18 10:52:03 UTC  
> Url: https://github.com/boostorg/intrusive/issues/86  

MSVC 17 and gcc 13 and 14 report the following:  
  
```  
include/boost/intrusive/detail/hash.hpp:5:27: warning: invalid UTF-8 character <f1> [-Winvalid-utf8]  
```  
  
Code point `00F1` has to be encoded in two bytes but here it's in a single byte.  
  
Note that I used `-std=c++26 -finput-charset=UTF-8` and `/std:c++20 /utf-8`.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-07-18 10:52:47 UTC  
> Url: https://github.com/boostorg/intrusive/issues/86#issuecomment-2236207526  

Thanks for the report!
