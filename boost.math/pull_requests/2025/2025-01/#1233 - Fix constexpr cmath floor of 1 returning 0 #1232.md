# #1233 Fix constexpr cmath floor of 1 returning 0 #1232 [Merged]

> Username: yousseflakhal  
> Created at: 2025-01-12 15:06:32 UTC  
> Updated at: 2025-01-13 13:49:08 UTC  
> Merged at: 2025-01-13 13:49:07 UTC  
> Closed at: 2025-01-13 13:49:07 UTC  
> Url: https://github.com/boostorg/math/pull/1233  

### Problem  
The `boost::math::ccmath::floor` function incorrectly returns `0.0` for `1.0` in a `constexpr` context.  
  
### Solution  
- Fixed the implementation to correctly handle `1.0`.  
- Added test cases in `ccmath_floor_test.cpp`.  
  
### Linked Issue  
Fixes #1232

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2025-01-12 17:56:20 UTC  
> Url: https://github.com/boostorg/math/pull/1233#issuecomment-2585850680  

Thanks for pressing on with this.  
  
I just approved this workflow run. We must approve workflow runs for first-time contributions @yousseflakhal.  
  
Let's let this thing run through CI now.  
  
Cc: @mborland and @jzmaddock

---

## Review 2 [Approved]

> Username: mborland  
> Created_at: 2025-01-13 13:48:54 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/1233#pullrequestreview-2546659919  

LGTM Thanks!

---
