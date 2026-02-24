# #1314 - set_bits::fp_traits_non_native warning for long double [Closed]

> Username: pps83  
> Created at: 2025-08-27 06:42:44 UTC  
> Updated at: 2025-08-29 18:25:36 UTC  
> Closed at: 2025-08-29 11:54:39 UTC  
> Url: https://github.com/boostorg/math/issues/1314  

```  
#include <boost/multiprecision/cpp_int.hpp>  
```  
with intel's compiler for windows I get this:  
  
```  
1>In file included from D:\work-pps\backtest-engine\src\test\Int128Test.cpp:1:  
1>In file included from D:\work-pps\boost_1_88_0\boost\multiprecision\cpp_int.hpp:19:  
1>In file included from D:\work-pps\boost_1_88_0\boost\multiprecision\number.hpp:12:  
1>In file included from D:\work-pps\boost_1_88_0\boost\multiprecision\detail\generic_interconvert.hpp:12:  
1>In file included from D:\work-pps\boost_1_88_0\boost\multiprecision\detail\default_ops.hpp:15:  
1>In file included from D:\work-pps\boost_1_88_0\boost\multiprecision\detail\fpclassify.hpp:16:  
1>In file included from D:\work-pps\boost_1_88_0\boost\math\special_functions\fpclassify.hpp:21:  
1>D:\work-pps\boost_1_88_0\boost\math\special_functions\detail\fp_traits.hpp(349,9): : warning : 'memcpy' will always overflow; destination buffer has size 2, but size argument is 4 [-Wfortify-source]  
1>  349 |         std::memcpy(reinterpret_cast<unsigned char*>(&x) + 6, &a, 4);  
```  
  
the code that produces the warning:  
```  
    static void set_bits(long double& x, uint32_t a)  
    {  
        std::memcpy(reinterpret_cast<unsigned char*>(&x) + 6, &a, 4);  
    }  
```  
sizeof(long double) is 8 with the compiler.  
  
https://www.boost.org/doc/libs/1_89_0/boost/math/special_functions/detail/fp_traits.hpp

---

## Comment 1

> Username: pps83  
> Created at: 2025-08-29 18:25:35 UTC  
> Url: https://github.com/boostorg/math/issues/1314#issuecomment-3237857812  

perhaps that code should have static_assert for long double size
