# #879 Fix -Wimplicit-const-int-float-conversion [Merged]

> Username: mborland  
> Created at: 2022-11-15 03:22:42 UTC  
> Updated at: 2022-11-16 02:45:25 UTC  
> Merged at: 2022-11-16 02:45:22 UTC  
> Closed at: 2022-11-16 02:45:22 UTC  
> Url: https://github.com/boostorg/math/pull/879  

Clang 15.0.4 issues the following warning:  
  
```  
In file included from ../../../boost/math/special_functions/beta.hpp:1603:  
../../../boost/math/special_functions/detail/ibeta_inverse.hpp:374:140: warning: implicit conversion from 'int' to 'float' changes value from 23128299 to 23128300 [-Wimplicit-const-int-float-conversion]  
   e3 -= (442043 * w_9 + 2054169 * w_8 + 3803094 * w_7 + 3470754 * w_6 + 2141568 * w_5 - 2393568 * w_4 - 19904934 * w_3 - 34714674 * w_2 - 23128299 * w - 5253353) * d / (146966400 * w_6 * w1_3);  
```

---
