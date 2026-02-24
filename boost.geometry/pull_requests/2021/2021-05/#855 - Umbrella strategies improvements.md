# #855 Umbrella strategies improvements [Merged]

> Username: awulkiew  
> Created at: 2021-05-24 19:45:09 UTC  
> Updated at: 2021-06-02 11:40:16 UTC  
> Merged at: 2021-06-02 11:40:15 UTC  
> Closed at: 2021-06-02 11:40:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/855  

This PR adds the following changes:  
- SeriesOrder template parameter is removed from geographic umbrella strategies  
- Umbrella strategy is propagated in `is_simple` and `is_valid`   
- `is_convex` umbrella strategy is added and supported in the algorithm (using the same side strategy as `convex_hull`)  
- `is_convex` for Polygon is implemented  
- Strategy getters (e.g. `get_point_in_geometry_strategy()`) are removed from legacy strategies  
- Default umbrella strategies are included with algorithms so the user no longer needs to include strategies explicitly if only the default one is needed.  
  
Todo (probably in a different PR or in this one if you insist):  
- Removal of strategy includes in tests when they are not needed (since the defaults are included with algorithms)  
- There is still one umbrella strategy missing for `remove_spikes` (`convex_hull` strategy could be reused for that, e.g. as an alias)  
- Aliases of umbrella strategies for all algorithms so they are not confusing and the user knows that e.g. `within` algorithm expects `within` strategy (right now `relate` strategy is used for all relops and setops).  
- Relocation of legacy strategies into `strategy/` directory  
  
Question:  
- should we use `side_robust` strategy in all algorithms by default, e.g. also in relops and setops?

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2021-05-28 11:10:20 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/855#pullrequestreview-671187512  

> Todo (probably in a different PR or in this one if you insist):  
>   
>     * Removal of strategy includes in tests when they are not needed (since the defaults are included with algorithms)  
>   
>     * There is still one umbrella strategy missing for `remove_spikes` (`convex_hull` strategy could be reused for that, e.g. as an alias)  
>   
>     * Aliases of umbrella strategies for all algorithms so they are not confusing and the user knows that e.g. `within` algorithm expects `within` strategy (right now `relate` strategy is used for all relops and setops).  
>   
>     * Relocation of legacy strategies into `strategy/` directory  
  
I am OK with leaving them for separate PR.   
  
> Question:  
>   
>     * should we use `side_robust` strategy in all algorithms by default, e.g. also in relops and setops?  
  
We should at least try. I can prepare a PR.

📁 include/boost/geometry/algorithms/is_convex.hpp

```diff
 175 |+         //   no solution or there would be two possible solutions - segment going through one of
 176 |+         //   the poles, at least in case of oblate spheroid, either way the answer would probably
 177 |+         //   be "false").
```

> Username: vissarion  
> Created_at: 2021-05-28 11:06:47 UTC  
> Updated_at: 2021-05-28 11:10:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/855#discussion_r641465810  

+1


---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2021-06-02 10:41:26 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/855#pullrequestreview-674020126  

Thanks for the huge PR!

📁 include/boost/geometry/algorithms/length.hpp

```diff
  49 |- #include <boost/geometry/strategies/default_length_result.hpp>
  50 |- 
  48 |+ #include <boost/geometry/strategies/default_length_result.hpp> // Move to algorithms
```

> Username: barendgehrels  
> Created_at: 2021-06-02 10:26:23 UTC  
> Updated_at: 2021-06-02 10:41:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/855#discussion_r643827565  

Can you mark this with `TODO` ?


📁 include/boost/geometry/strategies/buffer/cartesian.hpp

```diff
  42 |-                                     && util::is_segmental<Geometry2>::value
  43 |-                             > * = nullptr)
  36 |+                          distance::detail::enable_if_ps_t<Geometry1, Geometry2> * = nullptr)
```

> Username: barendgehrels  
> Created_at: 2021-06-02 10:27:32 UTC  
> Updated_at: 2021-06-02 10:41:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/855#discussion_r643828454  

:+1: Looks better than the 3 double conditions!


📁 include/boost/geometry/strategies/is_convex/cartesian.hpp

```diff
  35 |+                             <
  36 |+                                 util::is_pointlike<Geometry1>::value
  37 |+                              && util::is_pointlike<Geometry2>::value
```

> Username: barendgehrels  
> Created_at: 2021-06-02 10:31:58 UTC  
> Updated_at: 2021-06-02 10:41:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/855#discussion_r643831373  

This should probably be replaced by  
`distance::detail::enable_if_pp_t<Geometry1, Geometry2>` ?

> Username: barendgehrels  
> Created_at: 2021-06-02 10:32:24 UTC  
> Updated_at: 2021-06-02 10:41:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/855#discussion_r643831631  

But that is in `distance`, maybe it's more generic than that?

> Username: awulkiew  
> Created_at: 2021-06-02 10:58:40 UTC  
> Updated_at: 2021-06-02 10:59:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/855#discussion_r643849086  

The problem is that the naming correspond to the strategy, not geometries, e.g. enable_if_ps is true also for "segmental" geometries so any combination of linear and/or areal geometries. This is why I put it in `distance::detail`. But I agree that in general a utility like that could be useful. I'd only have to review all of the strategies and come up with proper names. But I'd prefer to do this in a different PR.

> Username: barendgehrels  
> Created_at: 2021-06-02 11:06:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/855#discussion_r643854089  

In another PR is fine.


---

## Comment 3

> Username: awulkiew  
> Created_at: 2021-06-02 11:39:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/855#issuecomment-852955230  

Thanks for the reviews.

---
