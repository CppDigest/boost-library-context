# #183 - cobalt::channel does not work with move only type [Closed]

> Username: dchansen  
> Created at: 2024-05-24 11:44:48 UTC  
> Updated at: 2024-05-27 08:17:21 UTC  
> Closed at: 2024-05-27 08:17:20 UTC  
> Url: https://github.com/boostorg/cobalt/issues/183  

The channel class requires that the value type be copyable, so things like unique_ptrs cannot be used with it.  
  
This example (for instance) does not compile:  
```cpp  
  
#include <boost/cobalt.hpp>  
#include <any>  
#include <memory>  
  
namespace bc = boost::cobalt;  
  
struct OnlyOne {  
    OnlyOne() = default;  
    OnlyOne(const OnlyOne&) = delete;  
    OnlyOne( OnlyOne&&) = default;  
};  
  
  
bc::task<void> test(){  
    bc::channel<OnlyOne> c;  
    co_await c.write(OnlyOne());         
}  
  
  
```  
See [godbolt](https://godbolt.org/z/hW6rGz78K)

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-05-27 02:13:25 UTC  
> Url: https://github.com/boostorg/cobalt/issues/183#issuecomment-2132523854  

Thanks for reporting. I created fixes for both your issues, can you check if they fix all problems?

---

## Comment 2

> Username: dchansen  
> Created at: 2024-05-27 08:17:20 UTC  
> Url: https://github.com/boostorg/cobalt/issues/183#issuecomment-2132913570  

Thanks for the quick response. Your patch fixed both issues.
