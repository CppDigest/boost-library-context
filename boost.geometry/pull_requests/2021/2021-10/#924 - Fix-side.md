# #924 Fix/side [Merged]

> Username: barendgehrels  
> Created at: 2021-10-13 13:10:33 UTC  
> Updated at: 2021-10-20 08:25:32 UTC  
> Merged at: 2021-10-20 08:25:28 UTC  
> Closed at: 2021-10-20 08:25:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/924  

This PR changes the default side strategy to `side_by_triangle` when rescaling is turned off.  
I first intended to change only the epsilon-strategy as discussed in the issue #918 .  
But that still gives several (around a dozen) regressions in difference, union and buffer. We maybe could live with that, or analyze them and try to fix it (I don't know yet the actual reason). And then use the `fp_equals_policy`. Please let me know your opinions.  
  
Additionally this PR extends several Jamfiles to test also the non-rescaling versions. This is to avoid these kind of regressions in the near future (in the longer future it will be the default anyway)

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2021-10-15 14:44:22 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/924#pullrequestreview-780921653  

I think this is OK for now, but we have to see what causes those issues. I will take a look next week.

📁 include/boost/geometry/strategies/agnostic/point_in_box_by_side.hpp

```diff
 193 |     static bool apply(Point const& point, Box const& box)
 194 |     {
 195 |+         using side_strategy_type = typename strategy::side::services::default_strategy<cartesian_tag, CalculationType>::type;
```

> Username: vissarion  
> Created_at: 2021-10-15 14:41:23 UTC  
> Updated_at: 2021-10-15 14:44:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/924#discussion_r729882502  

is this line too long?

> Username: barendgehrels  
> Created_at: 2021-10-17 19:45:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/924#discussion_r730466355  

thanks I'll fix it

> Username: barendgehrels  
> Created_at: 2021-10-20 08:25:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/924#discussion_r732530478  

:heavy_check_mark:


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-17 18:51:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/924#pullrequestreview-781479151  

📁 include/boost/geometry/strategies/agnostic/point_in_box_by_side.hpp

```diff
 122 |+             = typename strategy::side::services::default_strategy
 123 |+                 <cartesian_tag, CalculationType>::type;
 124 |+ 
```

> Username: awulkiew  
> Created_at: 2021-10-17 18:51:12 UTC  
> Updated_at: 2021-10-17 18:54:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/924#discussion_r730459455  

This is not a comment for this line but for this whole file.  
  
These xxx_by_side strategies are not used anywhere. In cartesian they are slower than simply checking if a point is inside a box and in spherical and geographic they return wrong result because edges of a box are neither great circles nor geodesics. This file could be removed entirely and was only left in case some user needed it for backward compatibility. ~Though it's probable that my umbrella strategies changes have broken that anyway.~ EDIT: it seems that strategy converter is specialized for them so they should be backward compatible.

> Username: barendgehrels  
> Created_at: 2021-10-20 08:24:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/924#discussion_r732530102  

Okay... good to know.  
So we should deprecate this file?


---
