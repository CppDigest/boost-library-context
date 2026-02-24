# #165 - [Tuple] Assignment is not marked as constexpr [Closed]

> Username: ldionne  
> Created at: 2015-08-28 16:16:18 UTC  
> Updated at: 2015-09-01 12:00:13 UTC  
> Closed at: 2015-09-01 12:00:13 UTC  
> Url: https://github.com/boostorg/hana/issues/165  

The following code fails to compile because the `closure`'s assignment operator is not marked `constexpr`:  
  
``` c++  
#include <boost/hana/tuple.hpp>  
  
#include <utility>  
namespace hana = boost::hana;  
  
  
constexpr int f() {  
    // copy-assign  
    {  
        hana::tuple<int> xs{1};  
        hana::tuple<long> ys{1};  
        ys = xs;  
    }  
  
    // move-assign  
    {  
        hana::tuple<int> xs{1};  
        hana::tuple<long> ys{1};  
        ys = std::move(xs);  
    }  
  
    return 0;  
}  
  
static_assert(f() == 0, ""); // force f() to be constexpr  
  
  
int main() { }  
```
