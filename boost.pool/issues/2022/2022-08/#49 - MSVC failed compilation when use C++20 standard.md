# #49 - MSVC failed compilation when use C++20 standard [Closed]

> Username: joelpelaez  
> Created at: 2022-08-26 01:48:31 UTC  
> Updated at: 2024-03-07 02:22:02 UTC  
> Closed at: 2024-03-07 02:22:02 UTC  
> Url: https://github.com/boostorg/pool/issues/49  

**Issue description:**  
Boost pool library has compilation issues with MSVC compiler on C++20 standard mode when the preprocessor macros _min_ and _max_ are defined. It can happens when some Windows specific headers are included to the project.  
  
Note: the files has already some mechanism to avoid this issues with other instances of _max_ calls.  
  
**Reproduce steps:**  
  
  
1. Use the next code:  
```  
#include <windows.h>  
#include <boost/pool/pool.hpp>  
  
using namespace std;  
  
int main()  
{  
    return 0;  
}  
```  
2. Compile it using MSVC compiler with c++20 standard with Boost headers only.  
  
**ErrorMessage:**  
Error	C2589	'(': illegal token on right side of '::'	D:\boost\include\boost-1_80\boost\pool\pool.hpp	363  
  
**Header code:**  
https://github.com/boostorg/pool/blob/600bcb027379b0670ccecf14f380f77e1264037f/include/boost/pool/pool.hpp#L363  
  
**Posible solution:**  
Using some mechanism to avoid the preprocessor convert max function to a preprocessor target.

---

## Comment 1

> Username: svart-riddare  
> Created at: 2022-08-30 09:33:26 UTC  
> Url: https://github.com/boostorg/pool/issues/49#issuecomment-1231416195  

You may also `#define NOMINMAX` before including `Windows.h`, preventing definition `min()` and `max()` macros by system headers.

---

## Comment 2

> Username: francoisk  
> Created at: 2023-02-15 09:50:36 UTC  
> Url: https://github.com/boostorg/pool/issues/49#issuecomment-1431036806  

> You may also `#define NOMINMAX` before including `Windows.h`, preventing definition `min()` and `max()` macros by system headers.  
  
The convention in Boost is apparently to use `BOOST_PREVENT_MACRO_SUBSTITUTION` (which `pool.hpp` already does in multiple places).

---

## Comment 3

> Username: mclow  
> Created at: 2024-03-07 02:22:02 UTC  
> Url: https://github.com/boostorg/pool/issues/49#issuecomment-1982218548  

This should be fixed by https://github.com/boostorg/pool/commit/ec7da07ed13e0c61e50d945b574a12ae7ec83cf4, please test with the 1.85.0 beta candidate (out tomorrow!) and let me know here if it's not fixed.
