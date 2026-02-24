# #46 - UB due to union based type punning [Closed]

> Username: Flamefire  
> Created at: 2019-08-23 06:43:55 UTC  
> Updated at: 2020-01-16 15:13:01 UTC  
> Closed at: 2020-01-16 15:13:01 UTC  
> Url: https://github.com/boostorg/intrusive/issues/46  

https://github.com/boostorg/intrusive/blob/3b5001f22f342e62a6f732b0bc58817a608a752f/include/boost/intrusive/detail/math.hpp#L215 uses an union to convert a float to an int. This has 2 problems each causing UB:  
  
1. a float may not be a IEEE754 float (I think this can be checked via defines)  
2. Accessing an inactive union member is UB (this can be fixed by a memcpy which is optimized away by the compiler)

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-01-16 15:07:39 UTC  
> Url: https://github.com/boostorg/intrusive/issues/46#issuecomment-575194318  

1. The implementation does not support anything that is not IEEE754, which is extremly rare there days, so this won't be fixed.  
  
2. As you suggest, a memcpy is the correct fix.
