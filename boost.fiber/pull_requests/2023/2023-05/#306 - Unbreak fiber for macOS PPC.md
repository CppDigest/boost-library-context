# #306 Unbreak fiber for macOS PPC [Merged]

> Username: barracuda156  
> Created at: 2023-05-14 09:08:49 UTC  
> Updated at: 2023-08-18 19:32:12 UTC  
> Merged at: 2023-08-18 05:21:29 UTC  
> Closed at: 2023-08-18 05:21:29 UTC  
> Url: https://github.com/boostorg/fiber/pull/306  

@olk Remember we were wondering why `context` was fixed but `fiber` failed? I preprocessed sources and found the cause. Of course this could not possibly work.

---

## Comment 1

> Username: barracuda156  
> Created_at: 2023-05-14 09:13:58 UTC  
> Url: https://github.com/boostorg/fiber/pull/306#issuecomment-1546851930  

Here are error we get with sources in `fiber`:  
```  
 /opt/local/bin/as -v -I . -arch ppc -o bin.v2/libs/fiber/build/darwin-12.2.0/release/threading-multi/visibility-hidden/algo/work_stealing.o bin.v2/libs/fiber/build/darwin-12.2.0/release/threading-multi/visibility-hidden/algo/work_stealing.s  
Apple Inc version cctools-localbuild, GNU assembler version 1.38  
bin.v2/libs/fiber/build/darwin-12.2.0/release/threading-multi/visibility-hidden/algo/work_stealing.s:2357:Parameter syntax error (parameter 1)  
```  
This is preprocessed source:  
```  
LVL172:  
	.loc 22 66 21  
; 66 "./boost/fiber/detail/spinlock_ttas.hpp" 1  
	or 27,27,27  
; 0 "" 2  
	.loc 22 66 63  
```  
Bingo.

---

## Comment 2

> Username: barracuda156  
> Created_at: 2023-05-25 10:58:50 UTC  
> Url: https://github.com/boostorg/fiber/pull/306#issuecomment-1562702868  

@olk Could you review this please?

---

## Comment 3

> Username: olk  
> Created_at: 2023-06-05 06:21:44 UTC  
> Url: https://github.com/boostorg/fiber/pull/306#issuecomment-1576116050  

> @olk Could you review this please?  
  
Unfortunately I don't own a Mac or PPC system.  
  
Did you run the unit tests in the subdirectory?

---

## Comment 4

> Username: barracuda156  
> Created_at: 2023-06-05 09:49:42 UTC  
> Url: https://github.com/boostorg/fiber/pull/306#issuecomment-1576480237  

@olk Not yet, but here it is merely a matter of wrong assembler syntax. On macOS registers must be prefixed with r (or f for float), so the existing code fails to build, and cannot build unless this is fixed.  
(I fixed the same bug in Catch2, in Seqan3 and elsewhere. Pretty common.)

---

## Comment 5

> Username: barracuda156  
> Created_at: 2023-08-18 05:14:07 UTC  
> Url: https://github.com/boostorg/fiber/pull/306#issuecomment-1683363415  

@olk Could we merge this? Without it `fiber` simply does not build at all.

---

## Comment 6

> Username: olk  
> Created_at: 2023-08-18 05:21:38 UTC  
> Url: https://github.com/boostorg/fiber/pull/306#issuecomment-1683368246  

ty

---
