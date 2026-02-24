# #105 - Undefined behavior in tanh-sinh tests: [Closed]

> Username: NAThompson  
> Created at: 2018-02-04 02:59:41 UTC  
> Updated at: 2018-04-22 18:20:39 UTC  
> Closed at: 2018-04-22 18:20:38 UTC  
> Url: https://github.com/boostorg/math/issues/105  

```  
Testing right limit infinite for tanh_sinh in 'A Comparison of Three High Precision Quadrature Schemes' on type boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u, int, void>, (boost::multiprecision::expression_template_option)1>  
/usr/local/include/boost/multiprecision/cpp_dec_float.hpp:434:34: runtime error: negation of -9223372036854775808 cannot be represented in type 'boost::long_long_type' (aka 'long long'); cast to an unsigned type to negate this value to itself  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /usr/local/include/boost/multiprecision/cpp_dec_float.hpp:434:34 in  
```  
  
Not sure if this is a bug in multiprecision or tanh-sinh, need to investigate more . . .

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-04-22 17:24:05 UTC  
> Url: https://github.com/boostorg/math/issues/105#issuecomment-383397707  

It's a bug in cpp_dec_float, testing a fix now.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-04-22 18:20:38 UTC  
> Url: https://github.com/boostorg/math/issues/105#issuecomment-383401543  

Should be fixed in develop.
