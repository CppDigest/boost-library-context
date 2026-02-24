# #1212 Fix many, many obvious test bugs [Merged]

> Username: rdoeffinger  
> Created at: 2024-10-15 11:23:51 UTC  
> Updated at: 2024-10-16 18:43:14 UTC  
> Merged at: 2024-10-16 15:16:12 UTC  
> Closed at: 2024-10-16 15:16:12 UTC  
> Url: https://github.com/boostorg/math/pull/1212  

Needed for tests to pass with 128 bit long double types

---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2024-10-15 13:23:30 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/1212#pullrequestreview-2369337044  

These changes all look reasonable to me. I approved the CI run.

---

## Comment 2

> Username: mborland  
> Created_at: 2024-10-16 15:16:03 UTC  
> Url: https://github.com/boostorg/math/pull/1212#issuecomment-2417137176  

Only CI failure is fail to clone on an s390x device (happens semi-regularly). Merging.

---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2024-10-16 15:38:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1212#pullrequestreview-2372903144  

📁 test/test_autodiff_2.cpp

```diff
 549 |     auto autodiff_v = atan2(make_fvar<T, m>(x), make_fvar<T, m>(y));
 539 |-     auto anchor_v = atan2(x, y);
 550 |+     auto anchor_v = atan2_wrap(x, y);
```

> Username: jzmaddock  
> Created_at: 2024-10-16 15:38:17 UTC  
> Updated_at: 2024-10-16 15:38:18 UTC  
> Url: https://github.com/boostorg/math/pull/1212#discussion_r1803363769  

Just curious: what was the issue here?  Surely the `using std::atan2` should take care of things?

> Username: rdoeffinger  
> Created_at: 2024-10-16 18:43:14 UTC  
> Url: https://github.com/boostorg/math/pull/1212#discussion_r1803625187  

Sorry, maybe I should have added a comment. The problem is the boost types (real_concept I think in this case), they do not add a std::atan2 overload, so it fails.  
There is a using std::atan2 but that has no effect in the presence of the C function, at least for this.  
Maybe there is a more clever way, or maybe boost should provide a single overload that works for everything so no such hacks are needed...


---
