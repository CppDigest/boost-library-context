# #50 - std::abs used without including header in extended_p_square.hpp [Open]

> Username: jefftrull  
> Created at: 2021-06-21 05:28:17 UTC  
> Updated at: 2021-06-21 05:32:36 UTC  
> Url: https://github.com/boostorg/accumulators/issues/50  

`std::abs` needs either `<cmath>` or `<cstdlib>` but neither is included. This results in a particularly hairy ambiguous overload problem on some old versions of gcc.  
  
It also may be better to use `std::fabs`, which is intended for [this use case](https://github.com/boostorg/accumulators/blob/14c13370602fe86d134a948943958cab0921ce9c/include/boost/accumulators/statistics/extended_p_square.hpp#L200)

---

## Comment 1

> Username: jefftrull  
> Created at: 2021-06-21 05:32:36 UTC  
> Url: https://github.com/boostorg/accumulators/issues/50#issuecomment-864740955  

[This stackoverflow answer](https://stackoverflow.com/a/34193863/192737) was particularly helpful for me
