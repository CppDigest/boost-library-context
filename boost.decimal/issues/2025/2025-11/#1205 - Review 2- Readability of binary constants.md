# #1205 - Review 2: Readability of binary constants [Closed]

> Username: jzmaddock  
> Created at: 2025-11-05 17:53:16 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2026-01-14 19:09:30 UTC  
> Url: https://github.com/boostorg/decimal/issues/1205  

Several binary literals throughout the codebase are difficult to read. For instance:  
  
  UINT64_C(0b10000000000000000000000000000000000000000000000000)  
  
might become:  
  
  UINT64_C(1) << 49  
  
  
This one is low priority, "nice to have" IMO.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-11-05 18:51:22 UTC  
> Url: https://github.com/boostorg/decimal/issues/1205#issuecomment-3492817178  

`C++14` and beyond support so-called digit separators. I usually use a single quote for separation. Groups of 3, 10, etc. digits are easier to read.
