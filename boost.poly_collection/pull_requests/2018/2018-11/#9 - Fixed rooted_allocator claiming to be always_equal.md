# #9 Fixed rooted_allocator claiming to be always_equal. [Closed]

> Username: BillyONeal  
> Created at: 2018-11-29 00:28:53 UTC  
> Updated at: 2018-12-22 21:29:02 UTC  
> Closed at: 2018-12-22 21:29:02 UTC  
> Url: https://github.com/boostorg/poly_collection/pull/9  

rooted_allocator is picking up std::allocator's operator==, operator!=, and is_always_equal==true by deriving from std::allocator, but it injects state that needs to be checked for equality. This change adds is_always_equal=false, and the missing comparison operators.

---

## Comment 1

> Username: joaquintides  
> Created_at: 2018-12-22 21:29:02 UTC  
> Url: https://github.com/boostorg/poly_collection/pull/9#issuecomment-449598635  

Thanks for the report. I've refactored the allocator handling code so as to support your use case and many other alternatives: 78e8a8e64b2d7eb98fe0c7030ca1667ddb7b0b6c...5af64d5ec21f3dbd5fccc025f50164bcaab0d886

---
