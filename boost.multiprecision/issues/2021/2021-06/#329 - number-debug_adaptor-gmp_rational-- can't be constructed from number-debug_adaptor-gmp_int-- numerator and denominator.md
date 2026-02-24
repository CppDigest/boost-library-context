# #329 - number<debug_adaptor<gmp_rational>> can't be constructed from number<debug_adaptor<gmp_int>> numerator and denominator [Closed]

> Username: rulatir  
> Created at: 2021-06-01 14:43:51 UTC  
> Updated at: 2021-06-12 08:01:57 UTC  
> Closed at: 2021-06-12 08:01:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/329  

As per subject, the numerator/denominator constructor of a `number<debug_adaptor<gmp_rational>>` does not accept arguments of type `number<debug_adaptor<gmp_int>>`. This necessitates jumping through a lot more hoops than should be necessary if one wants to write code that will use wrapped or naked backends depending on the `DEBUG` preprocessor variable.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-06-06 17:58:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/329#issuecomment-855437325  

Confirmed, all the "component-based" number types (rationals, complex, intervals) are basically broken with the logged/debug_adaptor.  
  
Working on fixes now.
