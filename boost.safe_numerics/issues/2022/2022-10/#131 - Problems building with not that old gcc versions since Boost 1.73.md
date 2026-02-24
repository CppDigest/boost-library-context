# #131 - Problems building with not that old gcc versions since Boost 1.73 [Open]

> Username: reddwarf69  
> Created at: 2022-10-10 14:03:42 UTC  
> Updated at: 2022-10-10 14:04:05 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/131  

Since https://github.com/boostorg/safe_numerics/commit/937928693d40564a72fd48f63039040908098132, building  
  
```  
#include <boost/safe_numerics/safe_integer.hpp>  
#include <fmt/format.h>  
#include <fmt/ostream.h>  
  
template<typename Stored,  
         Stored Min,  
         Stored Max,  
         typename P,  
         typename E>  
struct fmt::formatter<  
    boost::safe_numerics::safe_base<  
        Stored, Min, Max, P, E  
    >  
> : fmt::ostream_formatter  
{  
};  
  
void la()  
{  
    fmt::print("{}", boost::safe_numerics::safe<int>{ 4 });  
}  
  
```  
  
with gcc 8.2-10.4 with `-std=gnu++17 -Os -Wall` fails. https://godbolt.org/z/Goc3er7vc  
  
It's clearly a gcc bug, so it would be fair game to ignore it. But it affects Ubuntu 20.04.
