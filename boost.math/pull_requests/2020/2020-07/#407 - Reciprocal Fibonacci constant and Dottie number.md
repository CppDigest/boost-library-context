# #407 Reciprocal Fibonacci constant and Dottie number. [Merged]

> Username: NAThompson  
> Created at: 2020-07-24 18:48:42 UTC  
> Updated at: 2020-07-25 13:27:14 UTC  
> Merged at: 2020-07-25 13:26:39 UTC  
> Closed at: 2020-07-25 13:26:39 UTC  
> Url: https://github.com/boostorg/math/pull/407  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-07-24 18:59:01 UTC  
> Updated_at: 2020-07-24 19:01:14 UTC  
> Url: https://github.com/boostorg/math/pull/407#issuecomment-663685723  

@jzmaddock : Do you understand this performance behavior?  
  
```  
Dottie/512                       0.080 us        0.080 us     17392255  
Dottie/1024                      0.109 us        0.109 us     12780486  
Dottie/2048                      0.117 us        0.117 us     11917226  
Dottie/4096                      0.088 us        0.088 us     16042701  
Dottie/8192                      0.098 us        0.098 us     14220373  
Dottie/16384                     0.130 us        0.130 us     10628203  
Dottie/32768                     0.245 us        0.245 us      5869713  
Dottie/65536                   1723078 us      1722178 us            1  
Dottie/131072                  4771532 us      4769465 us            1  
Dottie/262144                 12902627 us     12895118 us            1  
```  
  
The jump at 65000 bits is pretty jarring. I also loosened the termination criteria to sqrt(eps) locally and nothing changed.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-07-25 11:54:26 UTC  
> Url: https://github.com/boostorg/math/pull/407#issuecomment-663846866  

@NAThompson : Do I understand your benchmark correctly that you're starting at 500 decimal digit accuracy and going up from there?  If so it will be calculating the constant fresh each time so we should be comparing like with like.  65536 decimal places is a heck of a lot of precision though - and a lot of memory allocation per mpfr_float instance.  Has it run the machine out of actual memory and started swapping like crazy?

---

## Comment 3

> Username: NAThompson  
> Created_at: 2020-07-25 13:26:07 UTC  
> Url: https://github.com/boostorg/math/pull/407#issuecomment-663855443  

> Do I understand your benchmark correctly that you're starting at 500 decimal digit accuracy and going up from there?  
  
That's correct; I guess I could start at 128 and still trigger the calculation each call.  
  
>  65536 decimal places is a heck of a lot of precision though - and a lot of memory allocation per mpfr_float instance. Has it run the machine out of actual memory and started swapping like crazy?  
  
The swapping might be the correct explanation, because I tried this benchmark on a machine with a smaller cache and saw the same behavior, but at 16,000 bits of precision.

---
