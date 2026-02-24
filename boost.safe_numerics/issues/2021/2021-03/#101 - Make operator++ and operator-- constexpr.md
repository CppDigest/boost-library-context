# #101 - Make operator++ and operator-- constexpr [Closed]

> Username: Eelis  
> Created at: 2021-03-27 16:24:14 UTC  
> Updated at: 2021-05-23 22:08:35 UTC  
> Closed at: 2021-05-23 22:08:35 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/101  

```  
#include <boost/safe_numerics/safe_integer.hpp>  
  
constexpr boost::safe_numerics::safe<unsigned int> f()  
{   
    boost::safe_numerics::safe<unsigned int> x = 3;  
    ++x; // error: call to non-constexpr operator++  
    return x;  
}  
```

---

## Comment 1

> Username: pratik-a  
> Created at: 2021-04-09 14:27:26 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/101#issuecomment-816722222  

#include <boost/safe_numerics/safe_integer.hpp>  
  
constexpr boost::safe_numerics::safe<unsigned int> f()  
{   
    boost::safe_numerics::safe<unsigned int> x = 3;  
   x++; // error: call to non-constexpr operator++  
    return x;  
}[

---

## Comment 2

> Username: robertramey  
> Created at: 2021-05-23 00:07:21 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/101#issuecomment-846479167  

fixed this in develop branch
