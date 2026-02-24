# #362 - The _c user-defined literal does not handle digit separators [Closed]

> Username: ldionne  
> Created at: 2017-07-10 13:49:32 UTC  
> Updated at: 2017-07-10 14:29:09 UTC  
> Closed at: 2017-07-10 14:29:09 UTC  
> Url: https://github.com/boostorg/hana/issues/362  

The following code does not behave as expected:  
  
```c++  
#include <boost/hana/integral_constant.hpp>  
using namespace boost::hana::literals;  
  
  
static constexpr auto x = 4'321'000_c;  
static_assert(decltype(x)::value == 4'321'000, "");  
  
int main() { }  
```  
  
Thanks to @josuttis for the report.
