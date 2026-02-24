# #907 - Build failed with  Apple clang version 15.0.0 (clang-1500.3.9.4) [Open]

> Username: xuvw  
> Created at: 2024-05-20 03:55:16 UTC  
> Updated at: 2024-08-20 14:33:41 UTC  
> Url: https://github.com/boostorg/boost/issues/907  

![20240520115402](https://github.com/boostorg/boost/assets/813855/0d393039-ff8d-48d2-a898-d29d2c3649a0)  
  
I get an build error. The log message is "integer value -1 is outside the valid range of values [0, 3] for this enumeration type" in integral_wrapper.hpp.   
Has any idea for this issue? Please help me, thanks.

---

## Comment 1

> Username: rpopescu  
> Created at: 2024-08-20 14:33:39 UTC  
> Url: https://github.com/boostorg/boost/issues/907#issuecomment-2299015340  

I believe it's the same as this https://github.com/boostorg/mpl/issues/69 and should be fixed in 1.86
