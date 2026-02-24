# #1294 - `round` and `lround` is not returning expected value for `0.999` [Closed]

> Username: edubart  
> Created at: 2026-01-10 21:24:11 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2026-01-12 18:06:16 UTC  
> Url: https://github.com/boostorg/decimal/issues/1294  

Test case:  
  
```c++  
#include <boost/decimal.hpp>  
#include <print>  
  
int main() {  
    using namespace boost::decimal::literals;  
    std::println("ceil {}", boost::decimal::ceil("0.999"_DD));  
    std::println("floor {}", boost::decimal::floor("0.999"_DD));  
    std::println("trunc {}", boost::decimal::trunc("0.999"_DD));  
    std::println("round {}", boost::decimal::round("0.999"_DD));  
    std::println("lround {}", boost::decimal::lround("0.999"_DD));  
    std::println("nearbyint {}", boost::decimal::nearbyint("0.999"_DD));  
    std::println("lrint {}", boost::decimal::lrint("0.999"_DD));  
}  
  
```  
  
Compiled with:  
```  
g++ test.cpp -o test -I./third-party/decimal/include -std=c++23 && ./test  
```  
  
Outputs on my machine (Linux x86_64):  
```  
ceil 1  
floor 0  
trunc 0  
round 0  
lround 0  
nearbyint 1  
lrint 1  
```  
  
I expected `round`/`lround` of `0.999` to result in `1` instead of `0`.  
I am using `gcc version 15.2.1 20251112`. I've tested both Boost.Decimal latest release and develop branch.
