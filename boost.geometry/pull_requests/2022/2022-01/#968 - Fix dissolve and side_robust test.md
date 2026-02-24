# #968 Fix dissolve and side_robust test [Merged]

> Username: awulkiew  
> Created at: 2022-01-18 18:05:50 UTC  
> Updated at: 2022-01-19 23:36:45 UTC  
> Merged at: 2022-01-19 23:36:45 UTC  
> Closed at: 2022-01-19 23:36:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/968  

Fix for https://github.com/boostorg/geometry/issues/944  
  
- fix compilation errors in dissolve caused by the addition of umbrella strategies  
- replace BOOST_FOREACH in tests  
- fix compilation errors in triangulation/side_robust test

---

## Comment 1

> Username: awulkiew  
> Created_at: 2022-01-18 18:09:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/968#issuecomment-1015679206  

@vissarion Should I move `side_robust` test from extensions to test/strategies?

---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2022-01-19 08:09:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/968#pullrequestreview-856429025  

Thanks, I am OK with merging.

---

## Review 3 [Approved]

> Username: barendgehrels  
> Created_at: 2022-01-19 09:26:53 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/968#pullrequestreview-856514209  

Super, thanks!

---
