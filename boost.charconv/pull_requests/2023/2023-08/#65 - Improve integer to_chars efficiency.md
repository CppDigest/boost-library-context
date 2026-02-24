# #65 Improve integer to_chars efficiency [Merged]

> Username: mborland  
> Created at: 2023-08-07 14:33:07 UTC  
> Updated at: 2023-08-07 15:33:42 UTC  
> Merged at: 2023-08-07 15:33:38 UTC  
> Closed at: 2023-08-07 15:33:39 UTC  
> Url: https://github.com/boostorg/charconv/pull/65  

With a 19 or 20 digit integer the last call to `decompose32` always recovered 2 digits. Replace call with a direct `memcpy` for exactly 2 digits.

---
