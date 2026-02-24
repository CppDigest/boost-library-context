# #29 Fix MSVC compilation [Merged]

> Username: glenfe  
> Created at: 2020-08-17 23:14:05 UTC  
> Updated at: 2020-09-02 02:08:13 UTC  
> Merged at: 2020-08-20 03:30:00 UTC  
> Closed at: 2020-08-20 03:30:00 UTC  
> Url: https://github.com/boostorg/heap/pull/29  



---

## Comment 1

> Username: timblechmann  
> Created_at: 2020-08-19 07:33:25 UTC  
> Url: https://github.com/boostorg/heap/pull/29#issuecomment-675905181  

@glenfe thanks a lot! especially for the appveyor integration. there's one build failure with msvc-12.0, i'm wondering if we might want to disable that configuration. any thoughts on this?

---

## Comment 2

> Username: glenfe  
> Created_at: 2020-08-19 13:43:26 UTC  
> Url: https://github.com/boostorg/heap/pull/29#issuecomment-676380524  

@timblechmann Since it's just the forward declarations in mutable_heap_test.cpp that MSVC 12 has a problem with, I've suppressed those in the unit test. This way we still test MSVC 12 for all the other supported Heap features.

---

## Comment 3

> Username: timblechmann  
> Created_at: 2020-08-19 15:40:27 UTC  
> Url: https://github.com/boostorg/heap/pull/29#issuecomment-676503163  

@glenfe great! i'll merge once the builds are green. thanks a lot!

---

## Comment 4

> Username: glenfe  
> Created_at: 2020-08-19 18:39:12 UTC  
> Url: https://github.com/boostorg/heap/pull/29#issuecomment-676593937  

@timblechmann Thanks (all green now).

---

## Comment 5

> Username: glenfe  
> Created_at: 2020-09-02 02:08:12 UTC  
> Url: https://github.com/boostorg/heap/pull/29#issuecomment-685240310  

Good to merge to master?

---
