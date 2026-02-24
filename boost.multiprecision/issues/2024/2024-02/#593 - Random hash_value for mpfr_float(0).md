# #593 - Random hash_value for mpfr_float(0) [Open]

> Username: fintarin  
> Created at: 2024-02-06 11:52:45 UTC  
> Updated at: 2024-02-06 11:52:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/593  

The hash_value for mpfr_float constructed from zero int is random. Thus, `hash_value(mpfr_float(0)) != hash_value(mpfr_float(0))`. The problem is related to `data()[0]._mpfr_d` - [code](https://github.com/boostorg/multiprecision/blob/1.84-standalone/include/boost/multiprecision/mpfr.hpp?plain=1#L1987).  
  
When debugging `hash_function(mpfr_float(0))` I get various random `_mpfr_d` such as:  
- `{0,8461470348308866388,7019261819594830956,7163369272283972460,8391161944126416233,8031153322401625088,8021807474101977202,8319401290704975468,7146776104407163694}`  
- `{0,7308619195232122478,7954879178731648110,7598805559586157151,3414698781512329070,3417225383054243439,8021807474102005619,8319401290704975468,7146776104407163694}`  
  
I assume that `_mpfr_d` holds an uninitialised values when the number is initialised with zero. So to fix this, you should check `is_zero` in the hash_value function. Also, the same problem can occur with other special values such as infinity and nan.
