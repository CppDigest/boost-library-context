# #361 Update Boost.Filesystem usage [Merged]

> Username: Lastique  
> Created at: 2024-01-26 21:37:50 UTC  
> Updated at: 2024-01-31 08:54:31 UTC  
> Merged at: 2024-01-31 02:40:38 UTC  
> Closed at: 2024-01-31 02:40:38 UTC  
> Url: https://github.com/boostorg/graph/pull/361  

This commit updates tests to avoid using Boost.Filesystem APIs that were deprecated and then removed.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2024-01-31 01:51:04 UTC  
> Url: https://github.com/boostorg/graph/pull/361#issuecomment-1918221926  

Any idea what's going on with GCC 9 on Linux?

---

## Comment 2

> Username: Lastique  
> Created_at: 2024-01-31 02:00:30 UTC  
> Url: https://github.com/boostorg/graph/pull/361#issuecomment-1918232259  

If you mean Drone then this is a `b2` issue [here](https://github.com/bfgroup/b2/issues/360). Reportedly fixed (but not yet merged to Boost).

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2024-01-31 02:40:12 UTC  
> Url: https://github.com/boostorg/graph/pull/361#issuecomment-1918266007  

OK, cool. Thanks for this!

---
