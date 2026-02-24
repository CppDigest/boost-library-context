# #278 - List of non BOOST-prefixed macros [Closed]

> Username: ned14  
> Created at: 2019-12-16 13:41:44 UTC  
> Updated at: 2019-12-21 19:29:32 UTC  
> Closed at: 2019-12-21 19:29:31 UTC  
> Url: https://github.com/boostorg/math/issues/278  

The following macros are missing a BOOST_ prefix, which is against Boost library guidelines:  
  
```  
./boost/math/special_functions/detail/airy_ai_bi_zero.hpp:#ifndef _AIRY_AI_BI_ZERO_2013_01_20_HPP_  
./boost/math/special_functions/detail/airy_ai_bi_zero.hpp:  #define _AIRY_AI_BI_ZERO_2013_01_20_HPP_  
./boost/math/special_functions/detail/airy_ai_bi_zero.hpp:#endif // _AIRY_AI_BI_ZERO_2013_01_20_HPP_  
./boost/math/special_functions/detail/bessel_jy_zero.hpp:#ifndef _BESSEL_JY_ZERO_2013_01_18_HPP_  
./boost/math/special_functions/detail/bessel_jy_zero.hpp:  #define _BESSEL_JY_ZERO_2013_01_18_HPP_  
./boost/math/special_functions/detail/bessel_jy_zero.hpp:#endif // _BESSEL_JY_ZERO_2013_01_18_HPP_  
```

---

## Comment 1

> Username: ckormanyos  
> Created at: 2019-12-16 21:29:57 UTC  
> Url: https://github.com/boostorg/math/issues/278#issuecomment-566252253  

Those are mine to fix.
