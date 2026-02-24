# #62 - is it possible to construct a new mpfr_float or mpc with a precision differing from default? [Closed]

> Username: ofloveandhate  
> Created at: 2018-08-09 21:03:29 UTC  
> Updated at: 2018-08-16 14:45:56 UTC  
> Closed at: 2018-08-16 07:52:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/62  

i'd like to be able to construct real scalars at a given precision which differs from default.  the purpose is generating random numbers at any given precision without changing the default to do it.  am i ➿ ?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-15 17:28:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/62#issuecomment-413272216  

I've pushed some fixes for this to develop, for mpfr_float you can now use a 2-arg constructor ( a value and a precision) and for mpc_complex a 3-arg constructor (real, imaginary and precision).  You can also use 2-arg copy-construction where the second arg is the precision (this one is only really useful for down-sampling the precision).  This was always intended, but got dropped due to overload resolution ambiguities and never reinstated.  I'm waiting for the CI tests to cycle, but it should all be working now.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-08-16 07:52:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/62#issuecomment-413458030  

CI passing, closing.

---

## Comment 3

> Username: ofloveandhate  
> Created at: 2018-08-16 14:45:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/62#issuecomment-413571175  

thankyou!!!!!!
