# #4 Fix compile-time error [Closed]

> Username: refi64  
> Created at: 2014-03-17 17:07:02 UTC  
> Updated at: 2014-03-18 09:38:15 UTC  
> Closed at: 2014-03-18 09:38:15 UTC  
> Url: https://github.com/boostorg/python/pull/4  

This fixes an error caused by boostorg/type_traits@d5c5988d92871f5474646dc4e9a6cb9d6809f460, where the broken_compiler_spec header was removed. It doesn't seem to actually be used, so I just removed that line.

---

## Comment 1

> Username: danieljames  
> Created_at: 2014-03-18 09:38:15 UTC  
> Url: https://github.com/boostorg/python/pull/4#issuecomment-37913832  

Duplicate of #3, and probably not needed any more.

---
