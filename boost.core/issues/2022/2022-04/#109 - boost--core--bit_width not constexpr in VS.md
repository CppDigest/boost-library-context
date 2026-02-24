# #109 - boost::core::bit_width not constexpr in VS [Closed]

> Username: joaquintides  
> Created at: 2022-04-21 07:11:14 UTC  
> Updated at: 2023-05-30 03:21:08 UTC  
> Closed at: 2023-05-30 03:21:08 UTC  
> Url: https://github.com/boostorg/core/issues/109  

The [following](https://godbolt.org/z/oeocqq3bo)  
```cpp  
#include <boost/core/bit.hpp>  
  
int x[boost::core::bit_width(16u)];  
```  
yields  
```  
<source>(3): error C3615: constexpr function 'boost::core::bit_width' cannot result in a constant expression  
C:/data/libraries/installed/x64-windows/include\boost/core/bit.hpp(445): note: failure was caused by call of undefined function or one not declared 'constexpr'  
C:/data/libraries/installed/x64-windows/include\boost/core/bit.hpp(445): note: see usage of 'boost::core::countl_zero'  
ASM generation compiler returned: 2  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2022-04-21 08:21:30 UTC  
> Url: https://github.com/boostorg/core/issues/109#issuecomment-1104859460  

Yes, because the MSVC bitscan intrinsics aren't constexpr.

---

## Comment 2

> Username: joaquintides  
> Created at: 2022-04-21 08:39:18 UTC  
> Url: https://github.com/boostorg/core/issues/109#issuecomment-1104880705  

Can't `std::is_constant_evaluated()` be leveraged to remedy this?

---

## Comment 3

> Username: pdimov  
> Created at: 2022-04-21 08:56:08 UTC  
> Url: https://github.com/boostorg/core/issues/109#issuecomment-1104904321  

`std::is_constant_evaluated` is C++20, so the gains will be marginal.

---

## Comment 4

> Username: joaquintides  
> Created at: 2022-04-21 08:57:33 UTC  
> Updated at: 2022-04-21 08:58:44 UTC  
> Url: https://github.com/boostorg/core/issues/109#issuecomment-1104906302  

This is better than nothing... At least the `BOOST_CONSTEXPR` qualifier should be conditionally removed in VS. Another possibility is to provide `constexpr_bit_width` etc. versions for those who need them.

---

## Comment 5

> Username: pdimov  
> Created at: 2022-04-21 08:59:53 UTC  
> Url: https://github.com/boostorg/core/issues/109#issuecomment-1104909732  

There's no need to remove the qualifier.  
  
It might be possible to use the is_constant_evaluated intrinsic: https://github.com/microsoft/STL/blob/5e3574b97985db31112a1c60e4eb52e821d25a83/stl/inc/xtr1common#L170  
  
This should be available in all language modes, but I'll need to check which MSVC versions have it.

---

## Comment 6

> Username: joaquintides  
> Created at: 2022-04-21 11:28:02 UTC  
> Url: https://github.com/boostorg/core/issues/109#issuecomment-1105086622  

Looks like the intrinsic is available from [Visual Studio 2019, versión 16.5](https://godbolt.org/z/ds3s8qGz1) onwards.
