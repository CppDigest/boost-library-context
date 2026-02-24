# #236 - BOOST_PARSER_NO_RUNTIME_ASSERTIONS fails compilation [Open]

> Username: psiha  
> Created at: 2025-05-05 21:14:59 UTC  
> Updated at: 2025-08-22 22:39:24 UTC  
> Url: https://github.com/boostorg/parser/issues/236  

https://godbolt.org/z/ETnc1hnfe

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-07-12 21:05:22 UTC  
> Url: https://github.com/boostorg/parser/issues/236#issuecomment-3066044918  

Could you try this again, building against develop/HEAD?  IDK if this got fixed somehow or what, but I cannot reproduce this locally.

---

## Comment 2

> Username: psiha  
> Created at: 2025-08-22 22:39:23 UTC  
> Url: https://github.com/boostorg/parser/issues/236#issuecomment-3215857941  

Fails the same with Boost 1.89.  
(even worse w/ 1.89 my existing code no longer compiles - huge indiscernible error messages - even tough Boost release notes mention no changes to this library :/)
