# #428 Ensure remainder has correct sign when the result is zero. [Merged]

> Username: jzmaddock  
> Created at: 2022-02-01 19:02:25 UTC  
> Updated at: 2022-02-02 15:25:39 UTC  
> Merged at: 2022-02-02 09:07:54 UTC  
> Closed at: 2022-02-02 09:07:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/428  

Also ensure float128 specializations of eval_signbit are actually called.  
Change return type of signbit to bool to match std::signbit and update docs.  
Add test case.  
Fixes https://github.com/boostorg/multiprecision/issues/426.

---

## Comment 1

> Username: cffk  
> Created_at: 2022-02-02 12:28:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/428#issuecomment-1027892833  

Thanks!

---
