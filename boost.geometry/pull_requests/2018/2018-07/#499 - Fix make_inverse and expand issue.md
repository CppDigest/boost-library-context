# #499 Fix make_inverse and expand issue  [Merged]

> Username: vissarion  
> Created at: 2018-07-06 13:08:56 UTC  
> Updated at: 2019-07-03 09:28:44 UTC  
> Merged at: 2018-07-09 11:39:37 UTC  
> Closed at: 2018-07-09 11:39:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/499  

This PR proposes a fix for issue https://github.com/boostorg/geometry/issues/498 by avoiding normalization for boxes created by ```make_inverse``` in spherical and geographic coordinate systems.

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2018-07-06 13:28:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/499#pullrequestreview-135013821  

📁 include/boost/geometry/util/is_inverse.hpp

```diff
  33 |+     bound_type low = bounds<bound_type>::lowest();
  34 |+ 
  35 |+     return math::equals(geometry::get<0, 0>(box), high) &&
```

> Username: awulkiew  
> Created_at: 2018-07-06 13:28:40 UTC  
> Updated_at: 2018-07-09 09:58:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/499#discussion_r200652960  

Only for 2d boxes?  
  
Btw, is it needed to call `math::equals`? Why not operators `<=` and `>=`? After assignment of the bounds we should be certain that these are the exact numbers. `math::equals` uses machine epsilon scaled by the number's magnitude so it's slower and not exact.

> Username: vissarion  
> Created_at: 2018-07-06 13:46:56 UTC  
> Updated_at: 2018-07-09 09:58:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/499#discussion_r200658272  

>Only for 2d boxes?  
  
its use is intended for spherical and geographic CS  
  
>Btw, is it needed to call math::equals? Why not operators <= and >=? After assignment of the bounds we should be certain that these are the exact numbers. math::equals uses machine epsilon scaled by the number's magnitude so it's slower and not exact.  
  
yes ```==``` is better

> Username: awulkiew  
> Created_at: 2018-07-06 16:45:31 UTC  
> Updated_at: 2018-07-09 09:58:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/499#discussion_r200709480  

Right, it's for testing longitude and latitude since the rest of the coordinates is treated like cartesian and handled in recursive template. And in this case and also for the first two coordinates in case of cartesian box the inverse case is handled automatically on per-coordinate basis. So I think that this tool should NOT be called `is_inverse()` because it is too general. The name should indicate that this is specifically for the spherical/geographic coordinates. Historically we add `spheroidal_coordinates` or something like this to the name.  
  
After valid coordinates are assigned they should no longer contain any of the bounds values. Why not use `OR` in the condition? If any of the coordinates is out of bounds just assign new point? If we wanted to be prepared for the case when only one of the coordinates e.g. max corner's longitude was set then we should check one coordinate at a time, one dimension at a time in expand(). And I think I'd prefer this, i.e. to have a tool in expand's details `set_or_expand<D, I>()` checking the coordinate explicitly for bounds if needed and seting or expanding in place. This way all coordinates would be independent from each other. Currently they are tied together.  
  
Side note: Both longitude and latitude are set at once because the code is prepared for the case of the change of both longitude and latitude during normalization. This is not needed in practice because the normlization is not done separately (normalization of both can be enabled with macro #define but I doubt anybody knows that). So we could simplify the code by agreeing that since now we will always normalize separately. In this case the coordinates could be handled one dimension at a time also in non-cartesian CSes. And in this case this tool should check only one dimension.

> Username: vissarion  
> Created_at: 2018-07-09 10:06:33 UTC  
> Updated_at: 2018-07-09 10:06:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/499#discussion_r200948545  

I changed the name of the utility to reflect the fact that it is used for spheroidal coordinates.   
  
Regarding comments on checking per dimension I agree but I think this PR is not the right place to implement such a general change and should be implemented in a separate PR that should also take care of other possible cases where spheroidal coordinates are threaded together. Also adding the utility under the ```detail::expand``` namespace is not a good idea as the code is right now since it is also used by ```envelope```.

> Username: awulkiew  
> Created_at: 2018-07-09 11:39:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/499#discussion_r200971010  

Ok, I agree that it should be done in separate PR, thanks.


---
