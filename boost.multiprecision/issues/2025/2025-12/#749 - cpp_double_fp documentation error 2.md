# #749 - cpp_double_fp documentation error 2 [Closed]

> Username: jrade  
> Created at: 2025-12-28 20:45:55 UTC  
> Updated at: 2025-12-29 17:05:06 UTC  
> Closed at: 2025-12-29 17:05:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/749  

The same bad header name as in issue [#747](https://github.com/boostorg/multiprecision/issues/747) can also be found in the table in  
https://www.boost.org/doc/libs/latest/libs/multiprecision/doc/html/boost_multiprecision/tut/floats.html

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-12-29 12:05:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/749#issuecomment-3696348066  

Hi @jrade thanks again for finding this one too. I will repair it also.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2025-12-29 12:08:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/749#issuecomment-3696353481  

See also #750

---

## Comment 3

> Username: jrade  
> Created at: 2025-12-29 12:56:55 UTC  
> Updated at: 2025-12-29 12:57:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/749#issuecomment-3696453918  

Also, the backend names in the first column in the table are not correct. The backends are  
  
cpp_bin_float<N>  
cpp_dec_float<N>  
gmp_float<N>  
mpfr_float_backend<N>  
float128_backend  
cpp_double_fp_backend<T>

---

## Comment 4

> Username: ckormanyos  
> Created at: 2025-12-29 13:29:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/749#issuecomment-3696521464  

> Also, the backend names in the first column in the table are not correct. The backends are  
  
Good catch again. It looks like the backend names you suggest correct 3 additional errors.  
  
I will correct accordingly.
