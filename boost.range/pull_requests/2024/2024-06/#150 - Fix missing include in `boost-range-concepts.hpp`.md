# #150 Fix missing include in `boost/range/concepts.hpp`. [Merged]

> Username: gogagum  
> Created at: 2024-06-09 15:01:46 UTC  
> Updated at: 2025-01-26 18:28:22 UTC  
> Merged at: 2024-10-10 22:10:32 UTC  
> Closed at: 2024-10-10 22:10:32 UTC  
> Url: https://github.com/boostorg/range/pull/150  

`boost::type_traits::is_integral` was used on line 188 without including its header.  
  
Fixes https://github.com/boostorg/range/issues/152

---

## Comment 1

> Username: Lastique  
> Created_at: 2025-01-26 15:29:33 UTC  
> Url: https://github.com/boostorg/range/pull/150#issuecomment-2614469869  

Please, merge this to master as well. Boost.Iterator currently needs this fix.

---

## Comment 2

> Username: neilgroves  
> Created_at: 2025-01-26 18:28:22 UTC  
> Url: https://github.com/boostorg/range/pull/150#issuecomment-2614539281  

On Sun, 26 Jan 2025 at 15:29, Andrey Semashev ***@***.***>  
wrote:  
  
> Please, merge this to master as well. Boost.Iterator currently needs this  
> fix.  
>  
Done.  
  
> Message ID: ***@***.***>  
>

---
