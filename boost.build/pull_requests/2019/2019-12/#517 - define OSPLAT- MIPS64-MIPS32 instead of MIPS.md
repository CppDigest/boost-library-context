# #517 define OSPLAT: MIPS64/MIPS32 instead of MIPS [Merged]

> Username: wzssyqa  
> Created at: 2019-12-18 11:25:00 UTC  
> Updated at: 2021-10-02 21:13:36 UTC  
> Merged at: 2019-12-26 15:23:28 UTC  
> Closed at: 2019-12-26 15:23:28 UTC  
> Url: https://github.com/boostorg/build/pull/517  

context need to define abi as o32 for MIPS32, while n64 for MIPS64.  
we need a way to know about it.

---

## Comment 1

> Username: olk  
> Created_at: 2019-12-18 12:45:28 UTC  
> Url: https://github.com/boostorg/build/pull/517#issuecomment-567016761  

Does it pass the unit tests?

---

## Comment 2

> Username: wzssyqa  
> Created_at: 2019-12-18 12:53:11 UTC  
> Url: https://github.com/boostorg/build/pull/517#issuecomment-567019138  

>   
>   
> Does it pass the unit tests?  
  
with this group of patch, I run unittest of context on mips64el, it works.  
In fact some test fails due to lack of -fsplit-stack support of the gcc for mips.

---
