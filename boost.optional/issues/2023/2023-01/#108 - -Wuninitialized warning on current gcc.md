# #108 - -Wuninitialized warning on current gcc [Closed]

> Username: roe85  
> Created at: 2023-01-09 15:06:05 UTC  
> Updated at: 2025-05-13 14:25:33 UTC  
> Closed at: 2025-05-13 14:25:33 UTC  
> Url: https://github.com/boostorg/optional/issues/108  

It seems that the `-Wuninitalized` warning which supposedly has been fixed is still active under certain conditions even on gcc trunk:   
[godbolt](https://gcc.godbolt.org/z/nnzbGKno5)  
  
```  
#include <boost/optional.hpp>  
  
boost::optional<int> bla()  
{  
    return {};  
}  
  
bool blub()  
{  
    return bla().has_value();  
}  
```  
Is this an issue of gcc or boost::optional?

---

## Comment 1

> Username: akrzemi1  
> Created at: 2023-12-29 00:46:28 UTC  
> Url: https://github.com/boostorg/optional/issues/108#issuecomment-1871648999  

This should be fixed by https://github.com/boostorg/optional/commit/e31cf6f2a8be437330f4547561df5ee8a62e4187
