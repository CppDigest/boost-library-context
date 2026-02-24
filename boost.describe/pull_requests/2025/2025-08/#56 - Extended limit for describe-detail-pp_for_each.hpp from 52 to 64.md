# #56 Extended limit for describe/detail/pp_for_each.hpp from 52 to 64 [Merged]

> Username: sigbjorn  
> Created at: 2025-08-08 08:54:07 UTC  
> Updated at: 2025-08-17 20:29:56 UTC  
> Merged at: 2025-08-17 07:18:55 UTC  
> Closed at: 2025-08-17 07:18:55 UTC  
> Url: https://github.com/boostorg/describe/pull/56  

Extend the limit to 64, including tests.

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-08-17 08:02:12 UTC  
> Url: https://github.com/boostorg/describe/pull/56#issuecomment-3194211664  

I'm not 100% positive that we can use `@` as a pp-token, but tests pass, so I'll leave it be. :-)

---

## Comment 2

> Username: sigbjorn  
> Created_at: 2025-08-17 20:29:56 UTC  
> Url: https://github.com/boostorg/describe/pull/56#issuecomment-3194633359  

Yes, you are right, the @ should be subst with something else later, it does not form a valid identifier if used, so the test only test the PP, in such a way we avoided the issue (lucky so far).

---
