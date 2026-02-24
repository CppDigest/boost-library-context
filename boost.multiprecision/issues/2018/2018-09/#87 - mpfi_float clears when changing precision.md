# #87 - mpfi_float clears when changing precision. [Closed]

> Username: MichaelABurr  
> Created at: 2018-09-19 22:20:42 UTC  
> Updated at: 2018-09-27 16:53:38 UTC  
> Closed at: 2018-09-27 16:53:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/87  

When changing the precision of mpfi's, the data in the mpfi is cleared.  This does not match the corresponding behavior for mpfr's.  In boost v1.66, this can be fixed by changing line 478 multiprecision/mpfi.hpp from mpfi_set_prec to mpfi_round_prec.  
  
Minimal example:  
  
#include<iostream>  
#include<boost/multiprecision/mpfi.hpp>  
  
int main() {  
     boost::multiprecision::mpfi_float my_interval(1);  
     my_interval.precision(10);  
     std::cout << my_interval << std::endl;  
return 0;}

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-09-27 16:53:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/87#issuecomment-425165411  

Should now be fixed in develop.
