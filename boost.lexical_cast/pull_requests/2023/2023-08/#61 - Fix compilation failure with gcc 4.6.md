# #61 Fix compilation failure with gcc 4.6 [Closed]

> Username: Lastique  
> Created at: 2023-08-25 15:35:48 UTC  
> Updated at: 2023-09-02 17:33:03 UTC  
> Closed at: 2023-09-02 14:43:17 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/61  

The compiler doesn't support explicit exception specifications in defaulted functions.  
  
Fixes https://github.com/boostorg/lexical_cast/issues/60.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2023-09-02 14:43:17 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/61#issuecomment-1703853812  

https://github.com/boostorg/lexical_cast/commit/af5ce2a5fe3321e2849a5ce89f80267c9059995e probably fixes the issue. Please reopen the ticket if not  
  
Thanks for the report and fix!

---

## Comment 2

> Username: Lastique  
> Created_at: 2023-09-02 17:32:18 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/61#issuecomment-1703895928  

> [af5ce2a](https://github.com/boostorg/lexical_cast/commit/af5ce2a5fe3321e2849a5ce89f80267c9059995e) probably fixes the issue. Please reopen the ticket if not  
>   
> Thanks for the report and fix!  
  
The bug report is https://github.com/boostorg/lexical_cast/issues/60, I have commented there. The problem is not fixed.

---
