# #68 - Missing #pragma warning(pop) on MSVC? [Closed]

> Username: mehrdadn  
> Created at: 2018-04-29 19:46:37 UTC  
> Updated at: 2018-05-16 17:00:08 UTC  
> Closed at: 2018-05-04 19:04:30 UTC  
> Url: https://github.com/boostorg/type_traits/issues/68  

It seems there might be some unmatched warning pushes/pops on Visual C++ [here](/boostorg/type_traits/blob/fb4430512d26681a7b06c434c793f98567a8ad4a/include/boost/type_traits/has_plus_assign.hpp#L30) and [here](/boostorg/type_traits/blob/e4d3cba6dd65d54e40751ed1e336cdd761620682/include/boost/type_traits/has_minus.hpp#L30).  
Note that there may be more—these are only the ones I've noticed.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-05-04 19:08:16 UTC  
> Url: https://github.com/boostorg/type_traits/issues/68#issuecomment-386703360  

Good catch: now fixed in develop, thanks!

---

## Comment 2

> Username: robhouse  
> Created at: 2018-05-15 14:04:50 UTC  
> Url: https://github.com/boostorg/type_traits/issues/68#issuecomment-389179068  

@jzmaddock - I believe the new #if block in has_minus.hpp should have been inserted after the next #endif. The same appears true for has_minus_assign.hpp and has_plus_assign.hpp as well.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-05-16 17:00:08 UTC  
> Url: https://github.com/boostorg/type_traits/issues/68#issuecomment-389592148  

Yep, fixed now - thanks!
