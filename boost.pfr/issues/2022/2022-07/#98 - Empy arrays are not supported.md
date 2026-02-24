# #98 - Empy arrays are not supported [Closed]

> Username: denzor200  
> Created at: 2022-07-27 20:04:46 UTC  
> Updated at: 2022-08-01 06:09:50 UTC  
> Closed at: 2022-08-01 06:09:50 UTC  
> Url: https://github.com/boostorg/pfr/issues/98  

**Sample**  
https://godbolt.org/z/c4WqTWv39  
  
**What i expect**  
0  
  
**What i get**  
```  
/opt/compiler-explorer/libs/boost_1_79_0/boost/pfr/detail/fields_count.hpp:317:87: error: static assertion failed: ====================> Boost.PFR: If there's no other failed static asserts then something went wrong. Please report this issue to the github along with the structure you're reflecting.  
  317 |         result != 0 || std::is_empty<type>::value || std::is_fundamental<type>::value || std::is_reference<type>::value,  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```

---

## Comment 1

> Username: denzor200  
> Created at: 2022-08-01 06:09:50 UTC  
> Url: https://github.com/boostorg/pfr/issues/98#issuecomment-1200753454  

Doesn't make sense, because ISO C++ forbids zero-size array
