# #207 - Using BOOST_REQUIRE () should not cause "comparison of integers of different" warning [Open]

> Username: k15tfu  
> Created at: 2019-02-25 21:20:07 UTC  
> Updated at: 2020-04-29 12:10:13 UTC  
> Url: https://github.com/boostorg/test/issues/207  

Hi! When I use BOOST_TEST_REQUIRE(==) the compiler generates "comparison of integers of different" warnings, but without BOOST_*() macros everything is compiled silently even with -Wall -Wextra.   
  
```C++  
  
#include <vector>  
  
#include <boost/test/unit_test.hpp>  
  
bool foo(std::vector<char>& v) {  
        BOOST_TEST_REQUIRE(v.size() == 0); // warning: comparison of integers of different signs: 'const unsigned long' and 'const int'  
        return v.size() == 0; // Okay  
}  
```
