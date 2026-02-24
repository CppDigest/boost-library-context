# #91 - Default precision used instead of current precision. [Closed]

> Username: MichaelABurr  
> Created at: 2018-10-04 01:32:03 UTC  
> Updated at: 2018-10-06 18:32:19 UTC  
> Closed at: 2018-10-06 18:31:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/91  

When one asks for the upper endpoint of an mpfi_float (and other similar computations), the corresponding mpfr_float is constructed using the default precision of an mpfr_float instead of the current precision of the mpfi_float.  This silent potential loss of precision does not seem be the desired behavior.  
  
Minimal Example:  
  
    #include<iostream>  
    #include<boost/multiprecision/mpfr.hpp>  
    #include<boost/multiprecision/mpfi.hpp>  
      
    int main() {  
    boost::multiprecision::mpfr_float my_mpfr;  
    boost::multiprecision::mpfi_float my_mpfi;  
      
    my_mpfi.precision(100);  
    my_mpfi = 10;  
      
    std::cout << my_mpfi << std::endl;  
    std::cout << my_mpfi.precision() << std::endl;  
      
    boost::multiprecision::mpfr_float::default_precision(50);  
    my_mpfr = upper(my_mpfi);  
      
    std::cout << my_mpfr << std::endl;  
    std::cout << my_mpfr.precision() << std::endl;  
      
    return 0;}

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-10-06 18:32:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/91#issuecomment-427596434  

That and other related issues should be fixed in develop now.  Thanks for spotting that!
