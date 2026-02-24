# #357 Add missing includes to graph headers [Merged]

> Username: vslashg  
> Created at: 2023-12-06 16:09:52 UTC  
> Updated at: 2023-12-07 05:07:30 UTC  
> Merged at: 2023-12-07 05:07:29 UTC  
> Closed at: 2023-12-07 05:07:29 UTC  
> Url: https://github.com/boostorg/graph/pull/357  

Some graph .hpp files are using APIs without including the associated headers.  This can cause compile errors if these headers are included first in a .cpp file.

---

## Comment 1

> Username: vslashg  
> Created_at: 2023-12-06 16:11:44 UTC  
> Url: https://github.com/boostorg/graph/pull/357#issuecomment-1843215009  

This likely isn't a huge deal in practice.  We do carry this patch locally, as our workflow verifies that header files build standalone, so I figured it was worth asking if upstream is interested in it.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2023-12-07 05:07:15 UTC  
> Url: https://github.com/boostorg/graph/pull/357#issuecomment-1844431395  

Always interested in patches that improve the quality of basically using the library! Thank you!

---
