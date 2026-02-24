# #30 - warning with -Wpedantic [Closed]

> Username: grisumbras  
> Created at: 2023-05-11 19:05:53 UTC  
> Updated at: 2023-05-12 11:13:08 UTC  
> Closed at: 2023-05-12 11:13:07 UTC  
> Url: https://github.com/boostorg/charconv/issues/30  

GCC complains with pedantic warnings enabled  
```  
include/boost/charconv/detail/integer_conversion.hpp|29 col 20| warning: ISO C++ does not support ‘__int128’ for ‘pack’ [-Wpedantic]  
```
