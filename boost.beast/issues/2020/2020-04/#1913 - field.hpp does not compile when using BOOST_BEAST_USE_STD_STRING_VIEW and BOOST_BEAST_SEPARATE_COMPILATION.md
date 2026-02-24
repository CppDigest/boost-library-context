# #1913 - field.hpp does not compile when using BOOST_BEAST_USE_STD_STRING_VIEW and BOOST_BEAST_SEPARATE_COMPILATION [Closed]

> Username: AlexandreBossard  
> Created at: 2020-04-22 21:21:38 UTC  
> Updated at: 2020-05-01 05:16:33 UTC  
> Closed at: 2020-05-01 05:16:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1913  

### Version of Beast  
  
using boost 1.71 and clang 9.0.0, version of clang seems irrelevant.  
  
### Steps necessary to reproduce the problem  
This does not compile with clang. you have to include `<ostream>` event if you compilation unit does not use it.  
  
https://godbolt.org/z/NtymZa  
```cpp  
// #include <ostream> // uncomment that to make it compile  
#include <boost/beast/http/field.hpp>  
int main()  
{  
    auto const f = to_string(boost::beast::http::field::content_type);  
}  
```  
  
This only happens when using both BOOST_BEAST_USE_STD_STRING_VIEW and BOOST_BEAST_SEPARATE_COMPILATION.  
  
I suppose moving the definition of the `operator<<(ostream &, field f)`[here](https://github.com/boostorg/beast/blob/f5064e0c460664fa34fdad44e2379a2309d39685/include/boost/beast/http/field.hpp#L403) to the definition file would solve the problem.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-04-22 21:35:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1913#issuecomment-618053452  

This program also fails:  
```  
#include <boost/beast/http/field.hpp>  
```
