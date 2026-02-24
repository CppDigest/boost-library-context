# #254 Enables -Wall and -Werror in CIs [Merged]

> Username: anarthal  
> Created at: 2025-05-20 11:45:28 UTC  
> Updated at: 2025-05-20 18:30:18 UTC  
> Merged at: 2025-05-20 18:30:15 UTC  
> Closed at: 2025-05-20 18:30:15 UTC  
> Url: https://github.com/boostorg/redis/pull/254  

Fixes some warnings due to unused variables, signed to unsigned comparisons, and superfluous moves

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-05-20 11:48:04 UTC  
> Url: https://github.com/boostorg/redis/pull/254#issuecomment-2894070941  

I've split this from #250 to make it more self-contained. -Werror here is useful if using Boost lightweight test (included in Boost.Core), which I think we should use here instead of Boost.Test (it's much more lightweight). In lightweight test, tests are just functions that get called from main(). Building with -Werror makes uncalled tests an error.  
  
In any case, it's useful that header-only libraries ship with as few warnings as possible, since they get propagated to user code.

---

## Comment 2

> Username: anarthal  
> Created_at: 2025-05-20 18:26:33 UTC  
> Url: https://github.com/boostorg/redis/pull/254#issuecomment-2895422614  

Merging as requested by Marcelo.

---
