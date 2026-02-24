# #11 move declaration of id2type_impl in 2-arg msvc_extract_type before co… [Closed]

> Username: mathcounts4  
> Created at: 2020-04-29 14:13:14 UTC  
> Updated at: 2021-12-23 01:36:53 UTC  
> Closed at: 2021-12-23 01:36:53 UTC  
> Url: https://github.com/boostorg/scope_exit/pull/11  

…mplete id2type_impl specialization

---

## Comment 1

> Username: mathcounts4  
> Created_at: 2020-04-29 14:19:37 UTC  
> Url: https://github.com/boostorg/scope_exit/pull/11#issuecomment-621241881  

This fixes https://github.com/boostorg/scope_exit/issues/9 but github won't let me attach it to this pull request

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2020-04-29 14:30:17 UTC  
> Url: https://github.com/boostorg/scope_exit/pull/11#issuecomment-621248830  

Wasn't that fixed by https://github.com/boostorg/scope_exit/pull/3? That code shouldn't be active at all with Visual Studio 2017+.

---

## Comment 3

> Username: mathcounts4  
> Created_at: 2020-04-29 15:00:41 UTC  
> Url: https://github.com/boostorg/scope_exit/pull/11#issuecomment-621268221  

> Wasn't that fixed by #3? That code shouldn't be active at all with Visual Studio 2017+.  
  
True. I saw this issue with boost 1.65.1 in VS2017 but no longer with boost 1.70 and VS2019.  
  
Would this change still be useful for VS2015-?

---

## Comment 4

> Username: mathcounts4  
> Created_at: 2021-12-23 01:36:53 UTC  
> Url: https://github.com/boostorg/scope_exit/pull/11#issuecomment-999980447  

Fixed by https://github.com/boostorg/scope_exit/pull/3 - closing this pull request

---
