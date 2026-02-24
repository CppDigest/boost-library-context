# #46 - Quite easy to fail build due to compile recursion issues [Closed]

> Username: Sunday111  
> Created at: 2020-08-11 09:17:17 UTC  
> Updated at: 2020-08-20 12:38:20 UTC  
> Closed at: 2020-08-20 12:38:20 UTC  
> Url: https://github.com/boostorg/pfr/issues/46  

```  
#include "boost\pfr\precise.hpp"  
  
struct test  
{  
    char data[256];  
};  
  
int main()  
{  
    test* val = new test;  
    return boost::pfr::get<0>(*val);  
}  
```  
  
  
` ...\boost\pfr\detail\fields_count.hpp(96): fatal error C1202: recursive type or function dependency context too complex`  
  
  
It also depends on the length of nested types names:  
  
```  
#include "boost\pfr\precise.hpp"  
  
struct test  
{  
    unsigned data[65];  
};  
  
int main()  
{  
    test* val = new test;  
    return (int)boost::pfr::get<0>(*val);  
}  
```  
  
```  
#include "boost\pfr\precise.hpp"  
#include <array>  
  
namespace geom  
{  
    template<typename T, size_t Rows, size_t Columns>  
    struct matrix  
    {  
        std::array<std::array<T, Rows>, Columns> Data;  
    };  
}  
  
  
struct test  
{  
    geom::matrix<float, 4, 4> data[4];  
};  
  
int main()  
{  
    test* val = new test;  
    return (int)boost::pfr::get<0>(*val);  
}  
```  
  
  
I understand that this example is synthetic but it is easy to achieve when you have big enough nested struct hierarchy (static array of matrices, etc). But it seems that this may be fixed (at least improved) by using fold expressions instead of recursion in implementation.

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-08-20 12:38:20 UTC  
> Url: https://github.com/boostorg/pfr/issues/46#issuecomment-677639331  

I've added better diagnostics for a close issue here:   
https://github.com/apolukhin/magic_get/blob/50d58a2e84b848bd72f68450fae4c551cae2b166/misc/generate_cpp17.py#L67-L71  
  
Docs were also updated and [BOOST_PFR_USE_STD_MAKE_INTEGRAL_SEQUENCE](http://apolukhin.github.io/magic_get/boost_pfr/configuration_macro.html) macro exposed. Try using it, it may help.  
  
Alas, that's all I can do. Please report the issue to the compiler developers if it keeps appearing and there's no static assert triggered during compilation.  
  
  
Many thanks for the report!
