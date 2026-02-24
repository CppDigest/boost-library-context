# #926 - finite_difference_derivative with 6th Order calc coeff error value [Open]

> Username: Swordhalo  
> Created at: 2024-07-03 03:39:58 UTC  
> Updated at: 2024-07-03 03:39:58 UTC  
> Url: https://github.com/boostorg/boost/issues/926  

Version: 1.85.0  
file path: boost\math\differentiation\finite_difference.hpp  
  
In function: finite_difference_derivative with 6th Order, calc step h by :  
```  
// Error: h^6f^(7)(x)/140 + 5|f(x)|eps/h  
      Real h = pow(eps / 168, static_cast<Real>(1) / static_cast<Real>(7));  
```  
The coeff number 168 was wrong acrooding to the comment expression below. The Number 168 seems to be calc by 140*6/5, which the correct coeff should be 140*5/6 = 350/3.   
Besides, it should be eps * Coeff instead of  eps / 168.  
The final expression of step h should be like:   
```  
Real h = pow(eps * 350 / 3 , static_cast<Real>(1) / static_cast<Real>(7));  
```  
  
And, every comment about ’eps^2/3, eps^4/5, eps^6/7' were not used in the code, which also attentioned in the website document.
