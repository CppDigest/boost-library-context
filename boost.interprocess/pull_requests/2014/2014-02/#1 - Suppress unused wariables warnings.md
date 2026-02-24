# #1 Suppress unused wariables warnings [Closed]

> Username: apolukhin  
> Created at: 2014-02-12 06:55:24 UTC  
> Updated at: 2014-07-02 07:17:10 UTC  
> Closed at: 2014-05-01 14:39:59 UTC  
> Url: https://github.com/boostorg/interprocess/pull/1  

On a platforms (Android) that do not define `POSIX_MADV_NORMAL` or `MADV_NORMAL` variables `mode_padv` and `mode_madv` won't be used. In that case compiler will produce a "unused variable" warning.  
  
This patch suppresses the "unused variable" warnings for `mode_padv` and `mode_madv`.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2014-05-01 14:39:59 UTC  
> Url: https://github.com/boostorg/interprocess/pull/1#issuecomment-41915525  

Closing this as it was fixed in commit:  
  
SHA-1: 65b5d34a04ce646a4ff34ce32bee52ba9e9954c9  
- Fixes #9835, typos and MSVC version offset_ptr branchless optimization fix.

---
