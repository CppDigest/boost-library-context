# #15 `function_ref` should not always reference qualify parameters [Closed]

> Username: davidstone  
> Created at: 2024-10-10 01:27:44 UTC  
> Updated at: 2024-12-17 21:55:26 UTC  
> Closed at: 2024-12-17 21:55:25 UTC  
> Url: https://github.com/boostorg/compat/pull/15  

By accepting `Args&&...`, a function that accepts its arguments by value is required to accept only rvalues. Instead, we should accept `Args...` (the exact argument types listed in the signature), which allows by-value parameters to copy from lvalue arguments.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-10-10 07:47:46 UTC  
> Url: https://github.com/boostorg/compat/pull/15#issuecomment-2404332881  

This change is probably correct but it should include a corresponding test (that fails before the change and passes after it.)

---

## Comment 2

> Username: cmazakas  
> Created_at: 2024-12-17 21:55:25 UTC  
> Url: https://github.com/boostorg/compat/pull/15#issuecomment-2549721103  

This has been addressed by https://github.com/boostorg/compat/pull/17  
  
Let me know if I missed anything or if we need to re-open this. Thank you, David.

---
