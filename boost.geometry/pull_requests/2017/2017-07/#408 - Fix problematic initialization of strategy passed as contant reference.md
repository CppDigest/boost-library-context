# #408 Fix problematic initialization of strategy passed as contant reference [Merged]

> Username: vissarion  
> Created at: 2017-07-04 08:23:46 UTC  
> Updated at: 2018-01-22 22:17:23 UTC  
> Merged at: 2017-07-04 14:02:53 UTC  
> Closed at: 2017-07-04 14:02:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/408  

The problem appears when side_calculator called from here: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/get_turn_info_for_endpoint.hpp#L430 does not initialize the side strategy (i.e. inters.get_side_strategy()) passed as cont&.   
  
To fix this the PR proposes to remove const& from the declaration of m_side_strategy member.  
  
Note that the issue is reported by using both gcc and clang (versions 4.8 and 3.4 respectively).

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2017-07-04 13:52:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/408#pullrequestreview-47870852  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_helpers.hpp

```diff
  47 |                            Qi const& qi, Qj const& qj, Qk const& qk,
  48 |-                            SideStrategy const& side_strategy)
  48 |+                            SideStrategy side_strategy)
```

> Username: awulkiew  
> Created_at: 2017-07-04 13:52:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/408#discussion_r125478266  

This `const&` should probably stay. Could you confirm that leaving it is ok?

> Username: vissarion  
> Created_at: 2017-07-05 07:35:21 UTC  
> Updated_at: 2017-07-05 12:15:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/408#discussion_r125572770  

I confirm that leaving const& is ok, so no need for future change.


---

## Comment 2

> Username: awulkiew  
> Created_at: 2017-07-04 14:02:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/408#issuecomment-312885974  

I'll merge now and add `const&` I think should be there. We shoudl still be able to fix it in the following week having a permission in case I'm wrong.

---
