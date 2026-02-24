# #1282 - macOS ARM64 -Wfortify-source error with Clang-20 [Closed]

> Username: mborland  
> Created at: 2025-06-30 13:50:03 UTC  
> Updated at: 2025-08-29 11:54:39 UTC  
> Closed at: 2025-08-29 11:54:39 UTC  
> Url: https://github.com/boostorg/math/issues/1282  

```  
../../../boost/math/special_functions/detail/fp_traits.hpp:474:9: error: 'memcpy' will always overflow; destination buffer has size 0, but size argument is 4 [-Werror,-Wfortify-source]  
  474 |         std::memcpy(reinterpret_cast<unsigned char*>(&x) + offset_, &a, 4);  
      |         ^  
1 error generated.  
```  
  
The comment on this structure is:   
```  
// IEEE extended double precision format with 15 exponent bits (128 bits)  
```  
  
Mac ARM64 has `sizeof(long double) == sizeof(double)` where as other ARM64 like the Graviton runners in the Drone CI have 128-bit long doubles
