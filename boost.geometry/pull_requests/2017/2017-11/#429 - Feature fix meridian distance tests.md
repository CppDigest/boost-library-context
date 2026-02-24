# #429 Feature fix meridian distance tests [Merged]

> Username: vissarion  
> Created at: 2017-11-01 19:08:44 UTC  
> Updated at: 2018-01-22 22:11:26 UTC  
> Merged at: 2017-11-02 00:34:12 UTC  
> Closed at: 2017-11-02 00:34:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/429  

This PL fixes few issues caused by merging both meridian distance formula and point segment formula to develop.   
  
Most notably the following are fixed by calling meridian distance formula  
-- thomas formula return 0 distance for point pairs (x,90)--(y,-90), now   
-- andoyer has low accuracy for points that are very close to each other  
-- for two antipodal points on equator the library was considering the geodesic to be part of equator while it is a meridian passing by the pole (see fixes in perimeter tests)

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2017-11-01 19:32:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/429#pullrequestreview-73591729  

📁 include/boost/geometry/formulas/distance_point_segment.hpp

```diff
  36 |-         template <typename, bool, bool, bool, bool ,bool> class Inverse,
  37 |-         template <typename, bool, bool, bool, bool> class Direct,
  40 |+         typename FormulaPolicy,
```

> Username: awulkiew  
> Created_at: 2017-11-01 19:32:34 UTC  
> Updated_at: 2017-11-01 21:41:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/429#discussion_r148361094  

What is the rationale behind this change? `FormulaPolicy` is conceptually a part of strategies not formulas. I get that this way the template parameter is simpler (even though the typedefs below are more complex) and that this way both inverse and direct formulas are tied together and consistent. But still this change makes the `distance_point_segment` formula's interface different that the interfaces of other formulas. Do you think that we should change the interfaces of all other formulas taking inverse and/or direct formulas now? Even if the answer was yes this change probably shouldn't be a part of this PR, but some different one. Or am I missing something?

> Username: awulkiew  
> Created_at: 2017-11-01 19:36:22 UTC  
> Updated_at: 2017-11-01 21:41:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/429#discussion_r148361900  

Ok, I see now that below you used strategies inside formula. I'm not sure if this is the correct thing to do. In this case I think it'd be better to move this formula to strategies and instead of formula it should be a part of a strategy.


---

## Comment 2

> Username: awulkiew  
> Created_at: 2017-11-02 00:34:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/429#issuecomment-341283861  

Thanks!

---
