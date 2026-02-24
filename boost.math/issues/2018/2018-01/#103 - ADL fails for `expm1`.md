# #103 - ADL fails for `expm1` [Closed]

> Username: NAThompson  
> Created at: 2018-01-29 23:56:07 UTC  
> Updated at: 2018-01-30 17:09:28 UTC  
> Closed at: 2018-01-30 17:09:28 UTC  
> Url: https://github.com/boostorg/math/issues/103  

```  
#include <boost/math/special_functions/expm1.hpp>  
// Alternatively, include:  
// #include <boost/math/special_functions/math_fwd.hpp>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
  
  
using boost::multiprecision::cpp_bin_float_50;  
  
int main()  
{  
    using std::expm1;  
    cpp_bin_float_50 x = 0.0000001;  
    cpp_bin_float_50 y = expm1(x);  
}  
```  
  
This yields the error:  
  
```  
fatal error: call to function 'expm1' that is neither visible in the template definition nor found by argument-dependent lookup  
...  
/include/boost/math/special_functions/expm1.hpp:318:46: note: 'expm1' should be declared prior to the call site or in an associated namespace of one of its  
      arguments  
inline typename tools::promote_args<T>::type expm1(T x)  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-01-30 08:41:36 UTC  
> Url: https://github.com/boostorg/math/issues/103#issuecomment-361515983  

Works for me with both gcc and msvc - do you have an outdated multiprecision lib by any chance?

---

## Comment 2

> Username: NAThompson  
> Created at: 2018-01-30 17:09:28 UTC  
> Url: https://github.com/boostorg/math/issues/103#issuecomment-361663698  

My bad; my environment was picking up the system boost 1.60, not the current boost 1.66 . . .
