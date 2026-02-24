# #271 - Missing include in math/tools/big_constant.hpp [Closed]

> Username: BluescreenProductions  
> Created at: 2019-11-12 11:03:20 UTC  
> Updated at: 2019-11-12 11:36:06 UTC  
> Closed at: 2019-11-12 11:36:06 UTC  
> Url: https://github.com/boostorg/math/issues/271  

Hello :)  
  
In the current boost version (1.71.0), the macro 'BOOST_MATH_HUGE_CONSTANT' defined in math/tools/big_constant.hpp requires the missing header:  
  
    #include <boost/type_traits/is_constructible.hpp>  
  
Thank you in advance!

---

## Comment 1

> Username: pabristow  
> Created at: 2019-11-12 11:36:06 UTC  
> Url: https://github.com/boostorg/math/issues/271#issuecomment-552856976  

Is fixed in develop branch so should be in upcoming release.  
  
#270 include <boost/type_traits/is_constructible.hpp>  
#include <boost/type_traits/is_convertible.hpp>  
#include <boost/type_traits/is_floating_point.hpp>  
  
but thanks for reporting.
