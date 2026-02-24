# #39 Fix SIMD x86 predef redefinition (on AMD cpus) and fix doc [Merged]

> Username: ccharly  
> Created at: 2015-12-08 10:31:47 UTC  
> Updated at: 2015-12-08 16:25:38 UTC  
> Merged at: 2015-12-08 16:25:38 UTC  
> Closed at: 2015-12-08 16:25:38 UTC  
> Url: https://github.com/boostorg/predef/pull/39  

I've noticed that `BOOST_HW_SIMD` was redefined when AMD SIMD extensions were detected. For example:  
  
``` c++  
#include <boost/predef.h>  
#include <iostream>  
  
int main() {  
  std::cout << BOOST_HW_SIMD << std::endl;  
  return 0;  
}  
```  
  
Using this command line (on a machine with an AMD cpu):  
  
``` shell  
g++ -mxop -mavx2 main.cpp -I predef/include/ && ./a.out  
```  
  
Was giving this warning:  
  
``` shell  
In file included from predef/include/boost/predef/hardware.h:14:0,  
                 from predef/include/boost/predef.h:20,  
                 from main.cpp:1:  
predef/include/boost/predef/hardware/simd.h:85:0: warning: "BOOST_HW_SIMD" redefined [enabled by default]  
predef/include/boost/predef/hardware/simd.h:81:0: note: this is the location of the previous definition  
50300000  
```  
  
I've also found some part of the documentation that were wrong... For example: `_X86` was `_x86` in some predef names (only in the documentation) and also some heading were invalid.  
  
Let me know if something is wrong with that PR. Thanks!

---
