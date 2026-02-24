# #209 Darwin make_ppc32: fix addi in l1 [Merged]

> Username: barracuda156  
> Created at: 2022-10-15 10:54:31 UTC  
> Updated at: 2022-10-16 12:07:03 UTC  
> Merged at: 2022-10-16 05:25:36 UTC  
> Closed at: 2022-10-16 05:25:36 UTC  
> Url: https://github.com/boostorg/context/pull/209  

Minor but needed fix to Darwin ppc32 code: https://github.com/boostorg/context/pull/204#issuecomment-1270061890  
  
Builds fine on 10.5.8, 10.6 PPC (10A190) and 10.6.8 Rosetta.  
Functionality: just built `folly` (which depends on context/coroutine) on 10.5.8 ppc with it with a bunch of dependents, so apparently all good.  
  
@olk Hopefully we are done at this, and Darwin PPC will finally work. Once merged here, I will bring it to Macports.

---

## Comment 1

> Username: olk  
> Created_at: 2022-10-16 05:25:40 UTC  
> Url: https://github.com/boostorg/context/pull/209#issuecomment-1279895219  

ty

---
