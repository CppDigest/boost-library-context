# #332 Add missing break to switch [Merged]

> Username: ams2990  
> Created at: 2023-03-27 17:37:58 UTC  
> Updated at: 2023-03-28 23:20:27 UTC  
> Merged at: 2023-03-28 23:20:18 UTC  
> Closed at: 2023-03-28 23:20:18 UTC  
> Url: https://github.com/boostorg/graph/pull/332  

Clang warns about this with `-Wimplicit-fallthrough`.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2023-03-28 02:58:28 UTC  
> Url: https://github.com/boostorg/graph/pull/332#issuecomment-1486132775  

Why not just add a `break` statement? Seems cleaner to me. After all, there is nothing in the `default` case that requires falling through into it.

---

## Comment 2

> Username: ams2990  
> Created_at: 2023-03-28 03:03:35 UTC  
> Url: https://github.com/boostorg/graph/pull/332#issuecomment-1486136390  

Done.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2023-03-28 23:20:27 UTC  
> Url: https://github.com/boostorg/graph/pull/332#issuecomment-1487729077  

Thanks for that!

---
