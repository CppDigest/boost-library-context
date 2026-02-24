# #62 prefix: detect armv8 via boost.predef [Merged]

> Username: timblechmann  
> Created at: 2020-06-23 03:12:39 UTC  
> Updated at: 2020-06-24 03:02:16 UTC  
> Merged at: 2020-06-24 03:02:15 UTC  
> Closed at: 2020-06-24 03:02:15 UTC  
> Url: https://github.com/boostorg/lockfree/pull/62  

@mishatal could you possibly check this on your CPU? i'd rather use boost.predef for detecting armv8

---

## Review 1 [Approved]

> Username: mishatal  
> Created_at: 2020-06-23 18:09:22 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/lockfree/pull/62#pullrequestreview-436033899  

Can confirm that is works on MSVC ARM64 compiler. My local tests that uses lockfree queue are also passed on ARM64 Amberwing CPU.

---
