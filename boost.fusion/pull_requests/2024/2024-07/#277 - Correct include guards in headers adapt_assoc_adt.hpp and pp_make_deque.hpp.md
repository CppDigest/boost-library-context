# #277 Correct include guards in headers adapt_assoc_adt.hpp and pp_make_deque.hpp [Merged]

> Username: kiri11  
> Created at: 2024-07-28 02:15:47 UTC  
> Updated at: 2024-07-29 02:19:51 UTC  
> Merged at: 2024-07-29 02:19:50 UTC  
> Closed at: 2024-07-29 02:19:51 UTC  
> Url: https://github.com/boostorg/fusion/pull/277  

When include guard parts `#ifndef`/`#define` don't match, that defeats the whole purpose of include guards which can lead to including the headers multiple times.  
  
Let's correct these errors and make the include guards work as intended.  
  
Referenced in issues:  
https://github.com/boostorg/fusion/issues/275  
https://github.com/boostorg/fusion/issues/276

---
