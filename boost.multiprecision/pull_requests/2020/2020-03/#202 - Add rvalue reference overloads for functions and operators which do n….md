# #202 Add rvalue reference overloads for functions and operators which do n… [Merged]

> Username: jzmaddock  
> Created at: 2020-03-12 19:33:06 UTC  
> Updated at: 2021-01-30 16:10:12 UTC  
> Merged at: 2020-04-06 21:04:20 UTC  
> Closed at: 2020-04-06 21:04:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/202  

…ot return expression templates.  
  
This avoids returning an expression template that references a temporary.  
Fixes: https://github.com/boostorg/multiprecision/issues/175.

---
