# #122 - Locale dependence on from_chars fall back [Closed]

> Username: mborland  
> Created at: 2024-01-17 12:31:02 UTC  
> Updated at: 2024-01-22 09:34:27 UTC  
> Closed at: 2024-01-22 09:34:27 UTC  
> Url: https://github.com/boostorg/charconv/issues/122  

In the instance that `from_chars` needs to use `strtold` in an non-"C" locale with a proper "C" locale string it's likely that the value will be returned incorrectly. Also probe `strtoflt128` since it's not documented, but I assume it matches strto*.

---

## Comment 1

> Username: mborland  
> Created at: 2024-01-17 13:32:29 UTC  
> Url: https://github.com/boostorg/charconv/issues/122#issuecomment-1895821812  

Test case:  
  
```  
#include <locale>  
#include <iostream>  
#include <cassert>  
#include <boost/charconv.hpp>  
  
int main(){  
    const char buffer[] = "1.189731495357231765e+4932";  
    std::locale::global(std::locale("de_DE.UTF-8"));  
      
    long double v = 0;  
    auto r = boost::charconv::from_chars( buffer,  
    buffer+sizeof(buffer), v );  
    assert(r);  
      
    std::locale::global(std::locale::classic());  
    long double v2 = 0;  
    auto r2 = boost::charconv::from_chars( buffer,  
    buffer+sizeof(buffer), v2 );  
    assert(r2);  
      
    std::cerr << v << "==" << v2 << std::endl;  
}  
```

---

## Comment 2

> Username: Lastique  
> Created at: 2024-01-17 14:59:07 UTC  
> Updated at: 2024-01-17 15:02:30 UTC  
> Url: https://github.com/boostorg/charconv/issues/122#issuecomment-1895988061  

Note there are `strtod_l` family of functions that receive a locale as input. There is also `uselocale` that sets the locale for the *current thread* rather than the whole process. This allows to use the C locale inside `from_chars`.
