# #3 New unit system <boost/units/systems/information.hpp> [Merged]

> Username: erikerlandson  
> Created at: 2014-06-18 21:20:28 UTC  
> Updated at: 2014-10-10 17:19:28 UTC  
> Merged at: 2014-10-10 17:19:28 UTC  
> Closed at: 2014-10-10 17:19:28 UTC  
> Url: https://github.com/boostorg/units/pull/3  

Defines base units and system units for: bit, byte, nat, hartley and shannon,  
and scale units for binary prefixes kibi, mebi, gibi, tebi, pebi, exbi.

---

## Comment 1

> Username: mkurdej  
> Created_at: 2014-08-01 06:53:42 UTC  
> Url: https://github.com/boostorg/units/pull/3#discussion_r15684151  

I would add as well zebi and yobi prefixes for completeness.  
  
``` cpp  
BOOST_UNITS_INFOSYS_PREFIX(70, zebi);  
BOOST_UNITS_INFOSYS_PREFIX(80, yobi);  
```

---

## Comment 2

> Username: mkurdej  
> Created_at: 2014-08-01 07:14:33 UTC  
> Url: https://github.com/boostorg/units/pull/3#discussion_r15684519  

And for completeness, one should add tests for zebi and yobi prefixes.  
Just one problem can arise, because `long long int` cannot represent numbers greater than `2^63 - 1`, so the test can pass with longer integer types only, e.g. int128_t.  
  
``` cpp  
#include <boost/multiprecision/cpp_int.hpp>  
  
using boost::multiprecision::int128_t;  
// using namespace boost::multiprecision::literals; // necessary if using user-defined literals  
  
quantity<info, int128_t> q70(1LL * zebi * byte);  
BOOST_CHECK_EQUAL(q70.value(), int128_t("1180591620717411303424"));  
// or equivalent C++11  
//BOOST_CHECK_EQUAL(q70.value(), 1180591620717411303424_cppi128);  
  
quantity<info, int128_t> q80(1LL * yobi * byte);  
BOOST_CHECK_EQUAL(q80.value(), int128_t("1208925819614629174706176"));  
// or equivalent C++11  
// BOOST_CHECK_EQUAL(q80.value(), 1208925819614629174706176_cppi128);  
```

---

## Comment 3

> Username: mkurdej  
> Created_at: 2014-09-16 14:14:12 UTC  
> Url: https://github.com/boostorg/units/pull/3#issuecomment-55748622  

This pull request can be closed, since #6 has been already merged.

---

## Comment 4

> Username: erikerlandson  
> Created_at: 2014-09-16 14:43:37 UTC  
> Url: https://github.com/boostorg/units/pull/3#issuecomment-55753452  

@mkurdej what branch was #3 merged into?

---

## Comment 5

> Username: mkurdej  
> Created_at: 2014-09-16 14:58:35 UTC  
> Url: https://github.com/boostorg/units/pull/3#issuecomment-55755908  

I was convinced that #6 (containing commit from #3) was merged into develop, but I've checked it again and it's actually a different commit that got merged in #6. I will take a closer look on it.  
Sorry for confusion.

---
