# #53 - Not enough constexpr [Closed]

> Username: pdimov  
> Created at: 2025-10-17 17:36:57 UTC  
> Updated at: 2025-10-18 06:11:45 UTC  
> Closed at: 2025-10-18 06:11:45 UTC  
> Url: https://github.com/boostorg/variant2/issues/53  

Currently this  
```  
#include <boost/variant2/variant.hpp>  
#include <meta>  
#include <any>  
  
consteval std::size_t f()  
{  
    boost::variant2::variant<std::meta::info, std::any> v( ^^int );  
    return v.index();  
}  
```  
fails: https://godbolt.org/z/j415eo8Mx  
  
but that's probably because constexpr destructors aren't supported, e.g. this  
```  
struct Y  
{  
    constexpr ~Y() {}  
};  
  
constexpr boost::variant2::variant<Y> v2;  
```  
fails too: https://godbolt.org/z/oP6xjG1bb  
  
There's a lot of missing `constexpr`, in general; it's because when the code was written the operations inside the functions couldn't possibly have been `constexpr`, but that's no longer the case in C++20 and subsequent.
