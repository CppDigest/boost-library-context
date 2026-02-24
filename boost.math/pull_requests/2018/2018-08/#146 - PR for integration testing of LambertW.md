# #146 PR for integration testing of LambertW [Merged]

> Username: jzmaddock  
> Created at: 2018-08-30 07:59:03 UTC  
> Updated at: 2018-12-28 01:38:58 UTC  
> Merged at: 2018-09-02 07:27:59 UTC  
> Closed at: 2018-09-02 07:27:59 UTC  
> Url: https://github.com/boostorg/math/pull/146  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2018-08-31 01:38:27 UTC  
> Url: https://github.com/boostorg/math/pull/146#issuecomment-417522060  

I didn't fix the linker error; I think it is due to test/Jamfile.v2 line 426, which should be removed.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2018-08-31 08:57:31 UTC  
> Url: https://github.com/boostorg/math/pull/146#issuecomment-417600128  

>I didn't fix the linker error; I think it is due to test/Jamfile.v2 line 426, which should be removed.  
  
There was some messed up preprocessor logic that I think was responsible.  Fingers crossed!

---
