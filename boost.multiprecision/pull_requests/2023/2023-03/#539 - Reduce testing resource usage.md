# #539 Reduce testing resource usage [Merged]

> Username: mborland  
> Created at: 2023-03-07 16:08:26 UTC  
> Updated at: 2023-03-08 23:17:53 UTC  
> Merged at: 2023-03-08 23:17:49 UTC  
> Closed at: 2023-03-08 23:17:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/539  

See: https://github.com/boostorg/math/issues/964

---

## Comment 1

> Username: mborland  
> Created_at: 2023-03-07 22:28:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/539#issuecomment-1458960362  

This dropped the total computational time in CI by ~15%. @jzmaddock is there anything else you can think of off hand that might be worth conditionally disabling to save resources?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2023-03-08 13:02:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/539#issuecomment-1460118533  

> This dropped the total computational time in CI by ~15%. @jzmaddock is there anything else you can think of off hand that might be worth conditionally disabling to save resources?  
  
Not off hand.  But we should probably have one MSVC and one clang runner running the MP tests too?

---

## Comment 3

> Username: mborland  
> Created_at: 2023-03-08 17:25:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/539#issuecomment-1460538016  

All three are covered with the latest commit.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2023-03-08 18:12:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/539#issuecomment-1460629666  

> All three are covered with the latest commit.  
  
Thanks!

---

## Comment 5

> Username: mborland  
> Created_at: 2023-03-08 23:17:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/539#issuecomment-1461019015  

Drone failure is on the CI system's end.

---
