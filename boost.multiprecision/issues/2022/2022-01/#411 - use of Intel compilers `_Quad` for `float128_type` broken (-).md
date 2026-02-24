# #411 - use of Intel compilers `_Quad` for `float128_type` broken (?) [Closed]

> Username: svigerske  
> Created at: 2022-01-14 09:48:37 UTC  
> Updated at: 2022-02-06 09:12:46 UTC  
> Closed at: 2022-02-06 09:12:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/411  

I tried to compile `float128.hpp` with  
```  
Intel(R) C++ Intel(R) 64 Compiler Classic for applications running on Intel(R) 64, Version 2021.2.0 Build 20210228_000000  
Copyright (C) 1985-2021 Intel Corporation.  All rights reserved.  
```  
on Windows and use flag `-Qoption,cpp,--extended_float_types -DBOOST_MP_USE_QUAD`.  
So I get  
```  
using float128_type = _Quad;  
```  
from `float128.hpp:49`.  
  
But there is code further down that uses `__float128` instead of `float128_type`:  
  
https://github.com/boostorg/multiprecision/blob/c6eb0536d739196b2236b469e2bfcff772a365e3/include/boost/multiprecision/float128.hpp#L156-L164  
  
This code does not compile:  
```  
boost/multiprecision/float128.hpp(146): error: identifier "__float128" is undefined  
  constexpr __float128 quad_min = static_cast<__float128>(1) * static_cast<__float128>(DBL_MIN) * static_cast<__float128>(DBL_MIN) * static_cast<__float128>(DBL_MIN) * static_cast<__float128>(DBL_MIN) * static_cast<__float128>(DBL_MIN) * static_cast<__float128>(DBL_MIN) * static_cast<__float128>(DBL_MIN) * static_cast<__float128>(DBL_MIN) * static_cast<__float128>(DBL_MIN) * static_cast<__float128>(DBL_MIN) * static_cast<__float128>(DBL_MIN) * static_cast<__float128>(DBL_MIN) * static_cast<__float128>(DBL_MIN) * static_cast<__float128>(DBL_MIN) * static_cast<__float128>(DBL_MIN) * static_cast<__float128>(DBL_MIN) / 1073741824;  
            ^  
```  
  
Is there a way to compile this with `_Quad` instead of `float128_type`, i.e., can one just replace `__float128` by `float128_type`?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-01-19 11:48:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/411#issuecomment-1016391040  

Unfortunately I've never managed to get Intel C++ running on CI so support has lapsed.  This should all work though, and yes, replace all occurrences of `__float128` by `float128_type`.  If that fixes things for you can I get you to check that libs/multiprecision/test/test_arithmetic_float_128.cpp builds and runs OK with the changes, and then submit the diff back to us either here or via pull-request?
