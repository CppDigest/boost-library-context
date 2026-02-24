# #50 Move floating point from chars detail into implementation header [Merged]

> Username: mborland  
> Created at: 2023-05-25 07:39:04 UTC  
> Updated at: 2023-05-25 12:43:12 UTC  
> Merged at: 2023-05-25 08:38:33 UTC  
> Closed at: 2023-05-25 08:38:33 UTC  
> Url: https://github.com/boostorg/charconv/pull/50  

As requested on slack

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-05-25 12:43:12 UTC  
> Url: https://github.com/boostorg/charconv/pull/50#issuecomment-1562841261  

This doesn't do anything - you're still including it in `from_chars.hpp`. It doesn't need to be there at all. It's only used in the implementation.

---
