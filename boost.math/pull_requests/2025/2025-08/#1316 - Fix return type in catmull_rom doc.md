# #1316 Fix return type in catmull_rom doc [Merged]

> Username: andrjohns  
> Created at: 2025-08-29 11:45:53 UTC  
> Updated at: 2025-08-29 11:50:04 UTC  
> Merged at: 2025-08-29 11:49:00 UTC  
> Closed at: 2025-08-29 11:49:00 UTC  
> Url: https://github.com/boostorg/math/pull/1316  

The doc for the [Catmull-Romm interpolator](https://www.boost.org/doc/libs/1_87_0/libs/math/doc/html/math_toolkit/catmull_rom.html) lists the return type for `operator()` as `Real` when it [should be `Point`](https://github.com/boostorg/math/blob/43239024883d0f4986bdfce4cb471b3563faf161/include/boost/math/interpolators/catmull_rom.hpp#L76)

---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2025-08-29 11:48:45 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/1316#pullrequestreview-3168448133  

Thank you!

---
