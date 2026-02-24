# #341 Remove dependencies on Boost.StaticAssert [Merged]

> Username: Lastique  
> Created at: 2026-01-22 16:39:58 UTC  
> Updated at: 2026-01-23 16:09:27 UTC  
> Merged at: 2026-01-23 15:08:41 UTC  
> Closed at: 2026-01-23 15:08:41 UTC  
> Url: https://github.com/boostorg/unordered/pull/341  

Boost.StaticAssert has been merged into Boost.Config, so remove the dependency.

---

## Comment 1

> Username: pdimov  
> Created_at: 2026-01-23 15:08:27 UTC  
> Url: https://github.com/boostorg/unordered/pull/341#issuecomment-3790677421  

Interestingly, the removal of `static_assert` should have caused failures because of the type_traits -> static_assert indirect dependency, but it doesn't. That's because we are actually no longer dependent on type_traits. I'll fix that after this merge.

---
