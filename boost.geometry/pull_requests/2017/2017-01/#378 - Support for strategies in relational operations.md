# #378 Support for strategies in relational operations. [Merged]

> Username: awulkiew  
> Created at: 2017-01-28 03:46:57 UTC  
> Updated at: 2017-02-17 22:42:13 UTC  
> Merged at: 2017-02-07 19:13:59 UTC  
> Closed at: 2017-02-07 19:13:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/378  

This PR adds support for strategies in relational operations. Strategies may be passed into the algorithm and are passed into the very bottom of the algorithm, through `get_turns`, `partition`, etc. This means that the interfaces of the internally used utilities are changed. `get_turns` and `turn_info` policies now take strategies. Partition now takes overlap and expand policies as function parameters instead of struct template parameters (this is not yet used but after adding `expand` and `disjoint` strategies for geographic Segments it will be needed).  
  
The interface of intersection strategies was simplified (policy was moved from class template parameter to apply function). This way the user passing the strategy is not forced to know the exact policy that has to be used. And it's possible to use different policies with the same intersection strategy. The agnostic winding strategy now contains CS-specific Side strategy member which may be defined by the user. The intersection strategy defines "lower-level" strategies that are used inside algorithms, i.e. side strategy and within Pt/Geometry strategy (winding) that are consistent with it.  
  
Previously a container of 2 strategies was used internally (containing side and intersection strategies). However IMO forcing the user to pass all of the required strategies (in some kind of umbrella strategy), that are consistent with each other correctly is not feasible. Plus if we wanted to add some algorithm internally using additional strategy it'd require to announce this breaking change. Not to mention that in various cases different sets of strategies are required and there may be more than 2 of them.  
  
For instance disjoint MultiPoint/Linear needs:  
 - Point/Segment covered_by strategy (by default `winding` strategy with Side strategy)  
 - `disjoint` Segment/Box strategy for `partition` (used for spatial partitioning)  
 - `expand`/`envelope` Segment for `partition`  
  
Another example, within Linestring/Polygon:  
 - Segment/Segment intersetion strategy with internally used Side strategy  
 - Point/Polygon `covered_by` strategy (by default `winding` with Side strategy)  
  
So in approach proposed in this PR it's required to pass the highest-level strategy only and the rest of the strategies is defined internally, consistently.  
  
An alternative could be to pass one strategy only but not define other strategies internally. Instead, other consistent strategies could be defined via some kind of traits.  
  
Another alternative could be to resign from strategies entirely and replace them with parameters. E.g. passing CS tag with formulas tags and definition of spheroid would result in creation of proper strategies automaically, internally in the algorithm. But that would require a lot more work.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2017-02-01 19:05:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/378#pullrequestreview-19628487  

📁 include/boost/geometry/algorithms/detail/disjoint/areal_areal.hpp

```diff
  43 |+ struct check_each_ring_for_within_call_covered_by
  44 |+ {
  45 |+     template <typename Point, typename PiGStrategy>
```

> Username: barendgehrels  
> Created_at: 2017-02-01 19:05:39 UTC  
> Updated_at: 2017-02-07 19:12:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/378#discussion_r98969248  

Hmm, PiGStrategy, funny name...  
Maybe just Strategy here? A comment could explain that it should be a Point-in-Geometry strategy

> Username: awulkiew  
> Created_at: 2017-02-01 23:54:11 UTC  
> Updated_at: 2017-02-07 19:12:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/378#discussion_r99026186  

Right, I'll change the name. :)


---

## Comment 2

> Username: barendgehrels  
> Created_at: 2017-02-01 19:09:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/378#issuecomment-276750510  

Thanks, this is really a big piece of work! I reviewed the first half and all changes are done in the same way. I'm OK with merging this (even if you keep PiGStrategy ;-) - OK for me if you want to )

---
