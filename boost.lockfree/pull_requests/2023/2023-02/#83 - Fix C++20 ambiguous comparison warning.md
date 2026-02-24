# #83 Fix C++20 ambiguous comparison warning [Merged]

> Username: guusw  
> Created at: 2023-02-24 06:17:27 UTC  
> Updated at: 2023-02-24 06:22:25 UTC  
> Merged at: 2023-02-24 06:22:20 UTC  
> Closed at: 2023-02-24 06:22:20 UTC  
> Url: https://github.com/boostorg/lockfree/pull/83  

Swapping lhs/rhs should be the same, so marking the function as volatile fixes this.  
  
This fixes:  
```  
warning: ISO C++20 considers use of overloaded operator '==' (with operand types 'A' and 'A') to be ambiguous despite there being a unique best viable function [-Wambiguous-reversed-operator]  
```

---

## Comment 1

> Username: timblechmann  
> Created_at: 2023-02-24 06:22:25 UTC  
> Url: https://github.com/boostorg/lockfree/pull/83#issuecomment-1442862751  

thanks!

---
