# #1314 - Loss of precision in operator% [Closed]

> Username: mborland  
> Created at: 2026-01-22 20:55:23 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2026-01-23 19:47:46 UTC  
> Url: https://github.com/boostorg/decimal/issues/1314  

Right now we are simply using:  
  
```c++  
const auto q_trunc {q > zero ? floor(q) : ceil(q)};  
        r = lhs - (q_trunc * rhs);  
```  
  
We should be able to scale the lhs and rhs significands, and compute the integer modulo, followed by constructing the resultant decimal type when possible
