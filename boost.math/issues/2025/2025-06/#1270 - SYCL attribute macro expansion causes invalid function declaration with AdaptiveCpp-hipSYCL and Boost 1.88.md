# #1270 - SYCL attribute macro expansion causes invalid function declaration with AdaptiveCpp/hipSYCL and Boost 1.88 [Closed]

> Username: Monstermel  
> Created at: 2025-06-10 03:06:57 UTC  
> Updated at: 2025-06-11 13:12:27 UTC  
> Closed at: 2025-06-11 13:12:27 UTC  
> Url: https://github.com/boostorg/math/issues/1270  

### Environment  
- Boost version: 1.88.0  
- AdaptiveCpp (hipSYCL) version: 25.02.0  
- C++ compiler: (e.g. clang++ 17)  
- Platform: (e.g. Linux x86_64)  
  
### Problem Summary  
Including SYCL headers (AdaptiveCpp/hipSYCL) before Boost.Math headers such as `boost/math/special_functions.hpp` leads to compilation errors due to macro expansion that places C++ attributes in invalid locations.  
  
This is caused by Boost’s `BOOST_MATH_GPU_ENABLED` macro, which expands to `SYCL_EXTERNAL`, which itself expands to a C++ attribute (`[[clang::annotate("hipsycl_sscp_outlining")]]`) between `inline` and the return type, which is not allowed in C++.  
  
### Minimal Reproducer  
```cpp  
#include "sycl/sycl.hpp"  
#include "boost/math/special_functions.hpp"  
  
int main() {  
  return 0;  
}  
```  
This fails to compile. If the include order is reversed (Boost first, then SYCL), it compiles successfully.  
  
### Full Error Output (excerpt)  
```  
/usr/local/include/boost/math/special_functions/fpclassify.hpp:89:19: error: an attribute list cannot appear here  
   89 | template<> inline BOOST_MATH_GPU_ENABLED bool (isnan)(float x) { return x != x; }  
      |                   ^~~~~~~~~~~~~~~~~~~~~~  
...  
```  
  
### Analysis  
- BOOST_MATH_GPU_ENABLED expands to BOOST_MATH_CUDA_ENABLED BOOST_MATH_SYCL_ENABLED  
- BOOST_MATH_SYCL_ENABLED expands to SYCL_EXTERNAL  
- SYCL_EXTERNAL expands to [[clang::annotate("hipsycl_sscp_outlining")]] in AdaptiveCpp  
- The attribute is placed between `inline` and the return type, which is not valid per the C++ standard.  
  
### Workaround  
- Including Boost headers before SYCL headers avoids macro expansion issues. However, this is only a workaround and not a proper fix, as include order should not affect compilation.  
  
### Expected  
- Boost and AdaptiveCpp should be compatible regardless of include order.  
- Macro expansion should not result in invalid attribute placement.

---

## Comment 1

> Username: mborland  
> Created at: 2025-06-10 12:22:47 UTC  
> Url: https://github.com/boostorg/math/issues/1270#issuecomment-2958996674  

This should be easy to fix since there's no reason to have an inline with a template anyway. I'll comb through the library for other instances as well. I saw your issue on multiprecision that will be fixed here.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2025-06-10 16:43:03 UTC  
> Url: https://github.com/boostorg/math/issues/1270#issuecomment-2959962335  

> This should be easy to fix since there's no reason to have an inline with a template anyway. I'll comb through the library for other instances as well. I saw your issue on multiprecision that will be fixed here.  
  
I'm not sure if that's true with the full specialization for isnan?  
  
There are also some critical functions which are `__force_inline` and will need special handling.

---

## Comment 3

> Username: mborland  
> Created at: 2025-06-10 17:28:58 UTC  
> Url: https://github.com/boostorg/math/issues/1270#issuecomment-2960079156  

> > This should be easy to fix since there's no reason to have an inline with a template anyway. I'll comb through the library for other instances as well. I saw your issue on multiprecision that will be fixed here.  
>   
> I'm not sure if that's true with the full specialization for isnan?  
  
They should still be implicitly inlined   
  
>   
> There are also some critical functions which are `__force_inline` and will need special handling.  
  
Those ones I'm unsure if the support exists. We can always `BOOST_MATH_FORCEINLINE` for non-GPU platforms

---

## Comment 4

> Username: jzmaddock  
> Created at: 2025-06-10 17:41:57 UTC  
> Url: https://github.com/boostorg/math/issues/1270#issuecomment-2960116277  

>They should still be implicitly inlined  
  
I just checked and you get ODR violations from multiple TU's if they're not explicitly marked inline.  In other words they're regular functions, not templates.

---

## Comment 5

> Username: mborland  
> Created at: 2025-06-10 17:45:30 UTC  
> Url: https://github.com/boostorg/math/issues/1270#issuecomment-2960127894  

> > They should still be implicitly inlined  
>   
> I just checked and you get ODR violations from multiple TU's if they're not explicitly marked inline. In other words they're regular functions, not templates.  
  
Great... I'll try re-ordering the annotations in that case
