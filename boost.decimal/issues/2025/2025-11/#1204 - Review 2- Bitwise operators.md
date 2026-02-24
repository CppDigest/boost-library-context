# #1204 - Review 2: Bitwise operators [Closed]

> Username: jzmaddock  
> Created at: 2025-11-05 17:52:14 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2026-01-14 17:51:09 UTC  
> Url: https://github.com/boostorg/decimal/issues/1204  

>Bitwise operators are defined but don't seem to be documented. A rationale for their inclusion would be nice too.  
  
These look surprising to me - you can't use bitwise operators on binary floating point, so why here?

---

## Comment 1

> Username: mborland  
> Created at: 2025-11-06 07:47:36 UTC  
> Url: https://github.com/boostorg/decimal/issues/1204#issuecomment-3495603361  

They are useful for a few implementation things and they do have tests in decTest. I just double checked IEEE 754 and there's no reference to them so they should probably get removed and add a few more friends.
