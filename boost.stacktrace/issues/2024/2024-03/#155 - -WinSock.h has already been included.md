# #155 - "WinSock.h has already been included" [Closed]

> Username: radj307  
> Created at: 2024-03-04 01:36:31 UTC  
> Updated at: 2024-03-06 10:36:12 UTC  
> Closed at: 2024-03-06 10:36:12 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/155  

The order in which you include `stacktrace.hpp` & `asio.hpp` can cause a compilation error on Windows.    
I tested this on MSVC 19.34.31947 with Boost 1.84.0.  
  
For example, this code doesn't compile:    
```cpp  
#include <boost/stacktrace/stacktrace.hpp>  
#include <boost/asio.hpp>  
```  
This is the error message:  
```  
boost\asio\detail\socket_types.hpp(24): fatal error C1189: #error:  WinSock.h has already been included  
```  
  
It compiles fine if you swap the order:  
```cpp  
#include <boost/asio.hpp>  
#include <boost/stacktrace/stacktrace.hpp>  
```  
  
The same code compiles without issue on Linux (tested with gcc 10 on Debian 11).    
This isn't really a huge problem as all you have to do to fix it is include `socket_types.hpp` before `stacktrace.hpp`, but the inconsistency is weird when cross-compiling.

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-03-06 10:36:12 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/155#issuecomment-1980567207  

Let's continue with this problem in https://github.com/chriskohlhoff/asio/issues/1441
