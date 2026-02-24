# #96 - construction, comparisons broken [Closed]

> Username: vinniefalco  
> Created at: 2021-09-06 00:02:18 UTC  
> Updated at: 2024-09-14 18:31:08 UTC  
> Closed at: 2024-09-14 18:31:08 UTC  
> Url: https://github.com/boostorg/optional/issues/96  

Lines 14 and 15 do not compile:  
https://godbolt.org/z/cejhMT6z8  
  
```  
#include <optional>  
#include <string_view>  
#include <boost/optional.hpp>  
#include <boost/utility/string_view.hpp>  
  
int main()  
{  
    int n = 0;  
  
    std::optional<std::string_view> v1 = "test";  
    if( v1 == "test" )  
        n |= 1;  
  
    boost::optional<std::string_view> v2 = "test";  
    if( v2 == "test ")  
        n |= 2;  
  
    return n;  
}  
```

---

## Comment 1

> Username: akrzemi1  
> Created at: 2024-09-13 14:36:52 UTC  
> Url: https://github.com/boostorg/optional/issues/96#issuecomment-2349114799  

This isn't really a bug. `boost::optional` works according to its specification. It is just it's specification is different than `std::optional`. `boost::optional` doesn't aim at 100% compatibility with `std::optional`, because the latter has design flaws that I do not want to repeat.   
  
For now, you need to trust me on this. I will add the section in the docs describing the differences between `boost::optional` and `std::optional` along with the rationale, and then hopefully this will be clear.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2024-09-13 17:44:52 UTC  
> Url: https://github.com/boostorg/optional/issues/96#issuecomment-2349637132  

That's fair, but in my mind this:  
```  
if( opt == X )  
```  
  
Should be a simple shortcut for  
  
```  
if( opt.has_value() && *opt == X )  
```  
  
Why shouldn't this work?

---

## Comment 3

> Username: akrzemi1  
> Created at: 2024-09-13 18:22:31 UTC  
> Url: https://github.com/boostorg/optional/issues/96#issuecomment-2349783439  

Not directly an answer to your question, but this should give you an idea about where I am coming from from:  
https://akrzemi1.wordpress.com/2018/12/09/deducing-your-intentions/

---

## Comment 4

> Username: vinniefalco  
> Created at: 2024-09-14 18:31:08 UTC  
> Url: https://github.com/boostorg/optional/issues/96#issuecomment-2351095281  

Peter doesn't like it either.
