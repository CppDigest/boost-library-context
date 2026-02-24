# #1232 - Constexpr cmath floor of 1 is returning 0 [Closed]

> Username: marcoffee  
> Created at: 2025-01-11 19:05:43 UTC  
> Updated at: 2025-01-13 13:49:08 UTC  
> Closed at: 2025-01-13 13:49:08 UTC  
> Url: https://github.com/boostorg/math/issues/1232  

`boost::math::ccmath::floor(1.0)` is returning `0.0` instead of `1.0`.  
Looks like the issue is [here](https://github.com/boostorg/math/blob/c5d36664fd9a15293ae3b319f71e51138c926b7c/include/boost/math/ccmath/floor.hpp#L36), when it compares the `arg` with `result` (which is `1`) and returns `0` if the `arg >= result`.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-01-11 21:20:54 UTC  
> Updated at: 2025-01-11 21:22:04 UTC  
> Url: https://github.com/boostorg/math/issues/1232#issuecomment-2585436480  

Hi @marcoffee thanks for your query.  
  
Hi Matt (@mborland) I'm working in this area now. What a coincidence.  
  
I wonder if `else if(result > arg)` on line 49 and a follow-up default case for exaclty 1 _might_ do the trick?

---

## Comment 2

> Username: ckormanyos  
> Created at: 2025-01-11 21:23:40 UTC  
> Url: https://github.com/boostorg/math/issues/1232#issuecomment-2585441147  

Or should we check for argument identical to 1?
