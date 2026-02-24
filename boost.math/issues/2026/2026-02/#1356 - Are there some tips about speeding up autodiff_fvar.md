# #1356 - Are there some tips about speeding up autodiff_fvar? [Open]

> Username: xuruilong100  
> Created at: 2026-02-04 02:41:16 UTC  
> Updated at: 2026-02-04 03:27:44 UTC  
> Url: https://github.com/boostorg/math/issues/1356  

I use a lot `autodiff_fvar` to calculate derivatives, are there some tips about speeding up `autodiff_fvar`?

---

## Comment 1

> Username: pulver  
> Created at: 2026-02-04 03:27:43 UTC  
> Url: https://github.com/boostorg/math/issues/1356#issuecomment-3845040583  

Couple ideas:  
  
- For `fvar<Real, N>` multiplication and division are both O(N²) so I'm not sure there's anyway around that (FFT can make it N log N in theory but probably won't be practical for N < 100) - maybe the compiler can be made to vectorize it?  
- If you're working with nonstandard functions (i.e. not in the autodiff lib) that you are able to calculate arbitrary derivatives for, you can write an autodiff-enabled version of it, which can often be faster (no guarantees). See section "Writing Functions for Autodiff Compatibility" in the [manual](https://www.boost.org/doc/libs/latest/libs/math/doc/differentiation/autodiff.pdf). One simple example is [cos()](https://github.com/boostorg/math/blob/develop/include/boost/math/differentiation/autodiff.hpp#L1546).
