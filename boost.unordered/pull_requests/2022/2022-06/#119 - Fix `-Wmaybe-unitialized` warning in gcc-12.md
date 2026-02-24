# #119 Fix `-Wmaybe-unitialized` warning in gcc-12 [Merged]

> Username: cmazakas  
> Created at: 2022-06-06 22:43:32 UTC  
> Updated at: 2022-06-07 20:19:15 UTC  
> Merged at: 2022-06-07 17:55:32 UTC  
> Closed at: 2022-06-07 17:55:32 UTC  
> Url: https://github.com/boostorg/unordered/pull/119  

Optimizations in gcc-12 seem to call out dubious usage of `reinterpret_cast` that hasn't been laundered. We add a c++03 compatible shim because of those same optimizations

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-06-06 23:00:45 UTC  
> Url: https://github.com/boostorg/unordered/pull/119#issuecomment-1148014350  

static_cast is "more correct" than reinterpret_cast, there's no need to change this for the worse.  
  
BOOST_UNORDERED_LAUNDER does not adhere to the naming convention for such macros, should be BOOST_UNORDERED_HAS_LAUNDER; and since it's only used locally, should be undefined afterwards.  
  
There's no need to check for C++17 and use std::launder in an `#ifdef _GLIBCXX_HAVE_BUILTIN_LAUNDER` block; if we're there we know that `__builtin_launder` is available, so just use that.

---

## Comment 2

> Username: cmazakas  
> Created_at: 2022-06-07 15:08:40 UTC  
> Url: https://github.com/boostorg/unordered/pull/119#issuecomment-1148803357  

Out of curiosity, why is `static_cast` better here? What's inappropriate about `reinterpret_cast` vs it?  
  
Ah, I think I can figure out why...  
  
Is it because we're not strictly reinterpreting something pointer interconvertible with the `function_pair`? I realize now, I likely broke the strict rules around reinterpret_cast'ing something because we're working with a simple POD type so this would be an aliasing violation.

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-06-07 17:53:38 UTC  
> Url: https://github.com/boostorg/unordered/pull/119#issuecomment-1148991451  

The result of `reinterpret_cast` is implementation-defined in the general case, whereas static casting to/from void* is not. In this specific case it probably doesn't matter, but downgrading the static_cast sequence is bad practice; there was nothing wrong with it.

---
