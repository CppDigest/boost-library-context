# #13 Embarcadero clang-based compilers predefine __clang__ also [Closed]

> Username: eldiener  
> Created at: 2020-03-22 04:32:39 UTC  
> Updated at: 2020-03-22 14:14:59 UTC  
> Closed at: 2020-03-22 14:14:58 UTC  
> Url: https://github.com/boostorg/assert/pull/13  



---

## Comment 1

> Username: pdimov  
> Created_at: 2020-03-22 12:52:10 UTC  
> Url: https://github.com/boostorg/assert/pull/13#issuecomment-602195671  

What is the purpose of this change?

---

## Comment 2

> Username: eldiener  
> Created_at: 2020-03-22 13:33:42 UTC  
> Updated_at: 2020-03-22 13:34:51 UTC  
> Url: https://github.com/boostorg/assert/pull/13#issuecomment-602201571  

The docs for the Embarcadero C++ clang-based compilers do not mention the \_\_PRETTY_FUNCTION\_\_ predefined macro, but do mention the \_\_FUNC\_\_ predefined macro, despite clang-based.

---

## Comment 3

> Username: pdimov  
> Created_at: 2020-03-22 13:40:12 UTC  
> Updated_at: 2020-03-22 13:40:43 UTC  
> Url: https://github.com/boostorg/assert/pull/13#issuecomment-602202522  

Unless `__PRETTY_FUNCTION__` really doesn't work, I think the code should stay as-is.

---

## Comment 4

> Username: eldiener  
> Created_at: 2020-03-22 14:14:58 UTC  
> Url: https://github.com/boostorg/assert/pull/13#issuecomment-602207108  

My error ! It does work as is, even with \_\_PRETTY_FUNCTION\_\_ not being documented in the Embarcadero help file. Believe the code, not the docs.

---
