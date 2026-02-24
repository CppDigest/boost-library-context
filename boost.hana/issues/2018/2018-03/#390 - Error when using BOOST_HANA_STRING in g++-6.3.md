# #390 - Error when using BOOST_HANA_STRING in g++-6.3 [Closed]

> Username: Bergmann89  
> Created at: 2018-03-05 21:36:25 UTC  
> Updated at: 2018-03-06 00:13:38 UTC  
> Closed at: 2018-03-05 21:45:06 UTC  
> Url: https://github.com/boostorg/hana/issues/390  

If I use BOOST_HANA_STRING with g++-6.3 I get the following error:  
  
**error**  
```  
In file included from ..../hana/include/boost/hana/detail/struct_macros.hpp:28:0,  
                 from ..../hana/include/boost/hana/adapt_adt.hpp:15,  
                 from ..../hana/include/boost/hana.hpp:59,  
                 from ..../main.cpp:4:  
..../hana/include/boost/hana/string.hpp:108:7: error: call to non-constexpr function ‘<lambda()>’  
     }()))                                                                   \  
       ^  
..../main.cpp:4:23: note: in expansion of macro ‘BOOST_HANA_STRING’  
 constexpr auto test = BOOST_HANA_STRING("fuuu");  
```  
  
**code to reproduce**  
```  
#include <iostream>  
#include <boost/hana.hpp>  
  
constexpr auto test = BOOST_HANA_STRING("fuuu");  
  
int main(int argc,char** argv)  
{  
    std::cout << boost::hana::to<const char*>(test) << std::endl;  
}  
```  
  
  
**g++ --version**  
```  
c++ (Ubuntu/Linaro 6.3.0-18ubuntu2~16.04) 6.3.0 20170519  
Copyright (C) 2016 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```

---

## Comment 1

> Username: ldionne  
> Created at: 2018-03-05 21:40:07 UTC  
> Url: https://github.com/boostorg/hana/issues/390#issuecomment-370575822  

The documentation for `BOOST_HANA_STRING` says:  
  
> This macro is a more convenient alternative to string_c for creating compile-time strings. However, since this macro uses a lambda internally, it can't be used in an unevaluated context.  
  
Before C++17, this also means that it can't be used where a constant expression is expected, because lambdas are not constexpr pre-17.

---

## Comment 2

> Username: Bergmann89  
> Created at: 2018-03-05 22:37:22 UTC  
> Updated at: 2018-03-05 22:38:38 UTC  
> Url: https://github.com/boostorg/hana/issues/390#issuecomment-370592653  

But I compiled with -std=c++17, so it should work, shouldn't it?  
€: Using clang-5.0 works without problems...

---

## Comment 3

> Username: ricejasonf  
> Created at: 2018-03-06 00:13:38 UTC  
> Url: https://github.com/boostorg/hana/issues/390#issuecomment-370614280  

According to this, constexpr lambdas were not available until g++ 7.0:  
https://gcc.gnu.org/projects/cxx-status.html  
  
Clang requires `-std=c++1z` for versions that didn't fully support the standard yet:  
```  
root@d83bdc0db12e:/opt/build# clang-4.0 -std=c++17 foo.cpp  
error: invalid value 'c++17' in '-std=c++17'  
```
