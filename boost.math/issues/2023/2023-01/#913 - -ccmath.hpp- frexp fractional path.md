# #913 - <ccmath.hpp> frexp fractional path? [Closed]

> Username: ckormanyos  
> Created at: 2023-01-08 10:47:59 UTC  
> Updated at: 2024-12-19 13:21:03 UTC  
> Closed at: 2024-12-19 13:21:02 UTC  
> Url: https://github.com/boostorg/math/issues/913  

Hi Matt (@mborland)  
  
I've recently used your fascinating work in `<ccmath.hpp>` as both a reference and inspiration for doing compile-time math in other Boost work.  
  
I find that the fractional path (i.e., for `arg < 0.5`) seems like it might be curiously missing from the `constexpr`-version of `frexp()`, as indicated [here](https://github.com/boostorg/math/blob/346d2a90f7f6f654794ad7d944e74eb7dc00378d/include/boost/math/ccmath/frexp.hpp#L30).  
  
A second trivial questoin is if the pointer needs a `nullptr` check in the zero-path [here](https://github.com/boostorg/math/blob/346d2a90f7f6f654794ad7d944e74eb7dc00378d/include/boost/math/ccmath/frexp.hpp#L25)?

---

## Comment 1

> Username: ckormanyos  
> Created at: 2023-01-08 10:57:26 UTC  
> Url: https://github.com/boostorg/math/issues/913#issuecomment-1374801204  

I'm playing around with a similar impl for Multiprecision [here](https://github.com/boostorg/multiprecision/blob/cpp_double_fp_backend_integration/include/boost/multiprecision/cpp_df_qf/cpp_df_qf_detail_ccmath_frexp.hpp).  
  
I can't use you Math impl out of the box for a few reasons like standalone and use of `<cmath>`funcs.  
  
But another issus arises. When using `Real` template param to be `float`, the numer $2^{32}$ has too many digits for lots of `float` implementations. Ultimately, I'd use a ternary choice for $2^{16}$ or $2^{48}$ and related constants depending on the `numeric_limits<Real>::digits` value.

---

## Comment 2

> Username: mborland  
> Created at: 2023-01-08 16:18:56 UTC  
> Url: https://github.com/boostorg/math/issues/913#issuecomment-1374872991  

You are correct that we should be checking for `nullptr` dereference to avoid UB.  
  
I'll take a look at your implementation and adjust accordingly.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2023-01-08 17:11:53 UTC  
> Url: https://github.com/boostorg/math/issues/913#issuecomment-1374883612  

>  look at your implementation...  
  
Thanks Matt (@mborland)  
  
Did I miss something for $|arg|<0.5$ in the implementation? What about fractional argument resulting in negative `expval`? Maybe I missed something simple somewhere obvious.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2024-12-19 13:21:02 UTC  
> Url: https://github.com/boostorg/math/issues/913#issuecomment-2553937096  

Hi Matt (@mborland) I seem to have made a mistake in this issue. The fractional path is available and seems like it was there from the beginning.  
  
Sorry for this disturbance. Closing.
