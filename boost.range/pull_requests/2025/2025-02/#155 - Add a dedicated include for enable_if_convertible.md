# #155 Add a dedicated include for enable_if_convertible [Merged]

> Username: Lastique  
> Created at: 2025-02-07 11:06:35 UTC  
> Updated at: 2025-02-08 16:32:29 UTC  
> Merged at: 2025-02-07 19:15:20 UTC  
> Closed at: 2025-02-07 19:15:20 UTC  
> Url: https://github.com/boostorg/range/pull/155  

`enable_if_convertible` is now defined in a separate header in Boost.Iterator. Also qualify iterators namespace for `enable_if_convertible`.

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-02-08 14:17:47 UTC  
> Url: https://github.com/boostorg/range/pull/155#issuecomment-2645732451  

Probably needs to be merged to master as well.

---

## Comment 2

> Username: Lastique  
> Created_at: 2025-02-08 15:09:53 UTC  
> Url: https://github.com/boostorg/range/pull/155#issuecomment-2645773512  

Yes, although it is not as necessary as the fix for the missing MPL includes. For now, `iterator_adaptor.hpp` still includes `enable_if_convertible.hpp` for backward compatibility, but eventually this include will be removed. It is better to just merge develop to master entirely.

---

## Comment 3

> Username: neilgroves  
> Created_at: 2025-02-08 16:32:28 UTC  
> Url: https://github.com/boostorg/range/pull/155#issuecomment-2645830882  

On Sat, 8 Feb 2025 at 15:10, Andrey Semashev ***@***.***>  
wrote:  
  
> Yes, although it is not as necessary as the fix for the missing MPL  
> includes. For now, iterator_adaptor.hpp still includes  
> enable_if_convertible.hpp for backward compatibility, but eventually this  
> include will be removed. It is better to just merge develop to master  
> entirely.  
>  
  
Boost.Range develop has been merged to master.  
  
Regards,  
Neil  
  
> Message ID: ***@***.***>  
>

---
