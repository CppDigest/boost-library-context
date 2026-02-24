# #449 - error: no member named 'setprecision' in namespace 'std' [Closed]

> Username: NAThompson  
> Created at: 2022-04-25 00:47:51 UTC  
> Updated at: 2022-04-26 05:12:16 UTC  
> Closed at: 2022-04-26 05:12:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/449  

In file included from main.cpp:2:  
../multiprecision/include/boost/multiprecision/gmp.hpp:3278:15: error: no member named 'setprecision' in namespace 'std'  
   os << std::setprecision(val.precision());  
         ~~~~~^  
1 error generated.  
  
Fix is `#include <iomanip>` in `gmp.hpp`.  
  
Discovered in standalone.

---

## Comment 1

> Username: NAThompson  
> Created at: 2022-04-25 00:50:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/449#issuecomment-1107956523  

Fixed in #450

---

## Comment 2

> Username: ckormanyos  
> Created at: 2022-04-26 05:12:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/449#issuecomment-1109351618  

Handled in #448
