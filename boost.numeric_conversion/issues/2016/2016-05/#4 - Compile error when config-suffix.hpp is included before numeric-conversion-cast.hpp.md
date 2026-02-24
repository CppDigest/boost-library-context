# #4 - Compile error when config/suffix.hpp is included before numeric/conversion/cast.hpp [Closed]

> Username: springmeyer  
> Created at: 2016-05-19 23:05:30 UTC  
> Updated at: 2018-03-28 13:21:08 UTC  
> Closed at: 2018-03-28 13:21:08 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/4  

The following testcase against boost 1.61.0:  
  
``` c++  
#include <boost/config/suffix.hpp>  
#include <boost/numeric/conversion/cast.hpp>  
  
int main() {  
    return 0;  
}  
```  
  
When compiled with clang++ on OS X with `-std=c++11` results in this compile error:  
  
``` c++  
/Users/dane/projects/node-osrm/mason_packages/.link/include/boost/type_traits/is_integral.hpp:75:38: error: no member named 'int128_type' in namespace 'boost'  
template<> struct is_integral<boost::int128_type> : public true_type{};  
                              ~~~~~~~^  
/Users/dane/projects/node-osrm/mason_packages/.link/include/boost/type_traits/is_integral.hpp:76:38: error: no member named 'uint128_type' in namespace 'boost'  
template<> struct is_integral<boost::uint128_type> : public true_type{};  
```  
  
When not compiled with `-std=c++11` then a different compile error happens:  
  
``` c++  
/Users/dane/projects/node-osrm/mason_packages/.link/include/boost/mpl/aux_/integral_wrapper.hpp:80:5: error: unknown type name 'constexpr'  
    BOOST_CONSTEXPR operator AUX_WRAPPER_VALUE_TYPE() const { return static_cast<AUX_WRAPPER_VALUE_TYPE>(this->value); }   
    ^  
/Users/dane/projects/node-osrm/mason_packages/.link/include/boost/config/suffix.hpp:951:25: note: expanded from macro 'BOOST_CONSTEXPR'  
#define BOOST_CONSTEXPR constexpr  
```  
  
Both of the compile errors can be worked around by changing the include order. This works fine:  
  
``` c++  
#include <boost/numeric/conversion/cast.hpp>  
#include <boost/config/suffix.hpp>  
  
int main() {  
    return 0;  
}  
```

---

## Comment 1

> Username: brandon-kohn  
> Created at: 2018-03-21 11:58:30 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/4#issuecomment-374913422  

I don't have access to an OSX build, can anyone confirm whether this still happens?

---

## Comment 2

> Username: brandon-kohn  
> Created at: 2018-03-28 13:21:08 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/4#issuecomment-376883964  

Closing the issue. Reopen when someone confirms it still happens.
