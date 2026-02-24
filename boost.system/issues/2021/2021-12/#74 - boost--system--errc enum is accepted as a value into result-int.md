# #74 - boost::system::errc enum is accepted as a value into result<int> [Closed]

> Username: ytimenkov  
> Created at: 2021-12-09 12:05:18 UTC  
> Updated at: 2021-12-11 02:06:34 UTC  
> Closed at: 2021-12-11 02:06:34 UTC  
> Url: https://github.com/boostorg/system/issues/74  

Boost version 1.78.0  
  
The shortest reproduction is:  
```c++  
#include <boost/system/result.hpp>  
  
static_assert(boost::system::result<int>{boost::system::errc::invalid_argument}.has_error());  
```  
```  
<source>:511:90: error: static assertion failed  
  511 | static_assert(boost::system::result<int>{boost::system::errc::invalid_argument}.has_error());  
      |               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~  
Compiler returned: 1  
```  
  
I use similar pattern extensively in the code with outcome and `std::error_code` , something like:  
```c++  
result<int> do_something(int count)  
{  
    if (count < 0)  
        return std::errc::invalid_argument;  
}  
```  
  
The fact that `errc` enum converts to value implicitly is really surprising (won't be surprised if it just didn't convert).

---

## Comment 1

> Username: pdimov  
> Created at: 2021-12-10 22:56:15 UTC  
> Url: https://github.com/boostorg/system/issues/74#issuecomment-991358204  

A better fix for this would be making `errc_t` a scoped enum (https://github.com/boostorg/system/issues/75), but for now, I'll disable the conversions to `int` on the `result` side.
