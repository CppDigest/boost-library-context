# #25 Fix GetTickCount return type [Merged]

> Username: MarcelRaad  
> Created at: 2014-09-28 19:34:21 UTC  
> Updated at: 2014-09-30 06:28:55 UTC  
> Merged at: 2014-09-29 18:18:38 UTC  
> Closed at: 2014-09-29 18:18:38 UTC  
> Url: https://github.com/boostorg/thread/pull/25  

int as a return type results in a signed/unsigned mismatch warning with MSVC (although disabled by default), and it doesn't even compile with clang-cl if the definiton from the Windows header was included before.  
  
Tested with MSVC 12, 14, and clang-cl.

---

## Comment 1

> Username: ned14  
> Created_at: 2014-09-29 18:18:41 UTC  
> Url: https://github.com/boostorg/thread/pull/25#issuecomment-57204535  

Great catch Marcel. I have no idea why I wrote the original that way :(

---

## Comment 2

> Username: viboes  
> Created_at: 2014-09-30 06:28:55 UTC  
> Url: https://github.com/boostorg/thread/pull/25#issuecomment-57272297  

Thanks Niall for taking care of this PR.

---
