# #56 Add test for GCC 12+ -Wmaybe-uninitialized false positive [Closed]

> Username: vinniefalco  
> Created at: 2025-12-31 13:59:05 UTC  
> Updated at: 2026-01-02 14:01:40 UTC  
> Closed at: 2026-01-02 14:01:40 UTC  
> Url: https://github.com/boostorg/variant2/pull/56  



---

## Comment 1

> Username: pdimov  
> Created_at: 2025-12-31 14:11:32 UTC  
> Url: https://github.com/boostorg/variant2/pull/56#issuecomment-3702277254  

This test doesn't emit any warnings under GCC 12+ in CI.

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2025-12-31 14:22:15 UTC  
> Url: https://github.com/boostorg/variant2/pull/56#issuecomment-3702292544  

Yes I'm trying to figure out how to reproduce it reliably. It might be the build settings?

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-12-31 14:40:23 UTC  
> Url: https://github.com/boostorg/variant2/pull/56#issuecomment-3702319591  

No, it's the test code. It's hard to trigger the warning; you need just the right amount of complexity and a lot of luck. That's because the warning only fires when the optimizer can see that the `exception_ptr` is uninitialized, but cannot see that `index()` is 0. And usually, it either sees both of these things, or none of them.

---

## Comment 4

> Username: pdimov  
> Created_at: 2025-12-31 14:51:17 UTC  
> Url: https://github.com/boostorg/variant2/pull/56#issuecomment-3702335582  

It's probably going to be easier to reproduce this by using `system::result` than `variant` directly, as it adds a layer of complexity.

---
