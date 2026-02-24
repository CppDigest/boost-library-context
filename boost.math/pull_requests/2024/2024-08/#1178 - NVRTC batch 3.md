# #1178 NVRTC batch 3 [Merged]

> Username: mborland  
> Created at: 2024-08-12 19:21:05 UTC  
> Updated at: 2024-08-14 14:21:17 UTC  
> Merged at: 2024-08-14 14:21:13 UTC  
> Closed at: 2024-08-14 14:21:13 UTC  
> Url: https://github.com/boostorg/math/pull/1178  

Adds support for: `numeric_limits`, `round`, `trunc`, `expm1`, `log1p`, and most importantly policy mechanisms  
  
Completed CI runs can be found at: https://github.com/cppalliance/cuda-math/pull/10  
  
Also deprecates miss-spelled overloads of `llconvert`: https://github.com/boostorg/math/commit/cb23c0756f194187a8398b346506599d2bc79f27. I assume this was a control replace error from some time ago.

---

## Comment 1

> Username: mborland  
> Created_at: 2024-08-14 14:21:08 UTC  
> Url: https://github.com/boostorg/math/pull/1178#issuecomment-2288918752  

Both of the CI failure are spurious. Should be safe to merge

---
