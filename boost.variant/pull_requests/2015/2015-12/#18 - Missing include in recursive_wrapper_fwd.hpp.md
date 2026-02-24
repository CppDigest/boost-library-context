# #18 Missing include in recursive_wrapper_fwd.hpp. [Merged]

> Username: jakobandersen  
> Created at: 2015-12-25 18:07:59 UTC  
> Updated at: 2015-12-29 20:20:26 UTC  
> Merged at: 2015-12-29 20:20:26 UTC  
> Closed at: 2015-12-29 20:20:26 UTC  
> Url: https://github.com/boostorg/variant/pull/18  

Add a missing include for mpl::true_/false_.  
Compiling  
  
``` C++  
#include <boost/variant/recursive_wrapper.hpp>  
int main() { }  
```  
  
with GCC gives  
  
```  
In file included from boost_1_60_0/boost/variant/recursive_wrapper.hpp:16:0,  
                 from test.cpp:1:  
boost_1_60_0/boost/variant/recursive_wrapper_fwd.hpp:54:1: error: expected class-name before ‘{’ token  
 {  
 ^  
boost_1_60_0/boost/variant/recursive_wrapper_fwd.hpp:60:1: error: expected class-name before ‘{’ token  
 {  
 ^  
```

---
