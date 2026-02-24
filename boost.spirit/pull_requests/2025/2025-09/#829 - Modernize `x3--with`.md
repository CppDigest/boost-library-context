# #829 Modernize `x3::with` [Merged]

> Username: saki7  
> Created at: 2025-09-11 10:31:31 UTC  
> Updated at: 2025-09-11 11:55:06 UTC  
> Merged at: 2025-09-11 11:54:37 UTC  
> Closed at: 2025-09-11 11:54:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/829  

Part of boostorg/spirit_x4#4   
  
- Use concepts in `x3::with`.  
- `x3::with` is now a CPO that inhibits ADL.  
- `x3::with_directive` now holds lvalue reference by reference, and rvalue by value. This is necessary for preventing dangling reference. However, passing dangling lvalue reference or destroying the value bound to the reference passed to `x3::with` technically can't be detected and it is the user's responsibility to not do such things.  
- Add `x3::with` test for all 4 value categories: `T`, `T const`, `T&`, `T const&`  
  
Old version silently created dangling reference when `x3::with<ID>(rvalue)` is assigned to an intermediate local variable. This is a security issue and should be immediately fixed.

---

## Comment 1

> Username: saki7  
> Created_at: 2025-09-11 11:55:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/829#issuecomment-3280208228  

Self-merged; this contains security fix and the tests are passing.

---
