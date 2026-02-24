# #165 Most members of std::allocate are deprecated in C++17 [Merged]

> Username: DanielaE  
> Created at: 2018-01-01 18:29:09 UTC  
> Updated at: 2018-01-14 06:34:23 UTC  
> Merged at: 2018-01-01 18:48:58 UTC  
> Closed at: 2018-01-01 18:48:58 UTC  
> Url: https://github.com/boostorg/fiber/pull/165  

And so is std::allocator<void>. Replace them by their cousins from std::allocator_traits. In addition to that, use std:allocator_traits wherever possible. Without that, heaps of deprecation warnings will fall onto humble users when compiling with MSVC 15 in C++17 mode.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: olk  
> Created_at: 2018-01-01 18:49:03 UTC  
> Url: https://github.com/boostorg/fiber/pull/165#issuecomment-354669066  

ty

---
