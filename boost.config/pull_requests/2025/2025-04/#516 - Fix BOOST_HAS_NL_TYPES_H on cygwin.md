# #516 Fix BOOST_HAS_NL_TYPES_H on cygwin [Open]

> Username: pps83  
> Created at: 2025-04-22 00:39:05 UTC  
> Updated at: 2025-04-22 00:44:36 UTC  
> Url: https://github.com/boostorg/config/pull/516  

Starting from CYGWIN_VERSION_API_MINOR=325 `catclose`, `catgets`, `catopen` are exported (see `<cygwin/version.h>` [line 455](https://github.com/msys2/msys2-runtime/blob/3819160299787e0ca64098a3e8945853291b135d/winsup/cygwin/include/cygwin/version.h#L455))

---
