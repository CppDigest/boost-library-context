# #311 Fix cross_track_point_box distance strategy [Merged]

> Username: mkaravel  
> Created at: 2015-06-12 22:58:17 UTC  
> Updated at: 2015-06-21 21:13:15 UTC  
> Merged at: 2015-06-21 21:13:11 UTC  
> Closed at: 2015-06-21 21:13:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/311  

This PR fixes the implementation of the `cross_track_point_box` distance strategy, so that it accounts for the fact that boxes in the spherical equatorial coordinate system are now defined as Cartesian products of intervals of longitudes and latitudes.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-06-18 12:06:07 UTC  
> Updated_at: 2015-06-19 06:38:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/311#discussion_r32722527  

I see a possibility for improvements here:  
- The closer edge could be found and then the distance calculated, only one.  
- If the point latitude was in [lat_min, lat_max] the distance could be calculated as e.g. `radius() * (plon - lon_max)`. The longitudes would have to be normalized somehow but in general we should be able to avoid calling a strategy.  
  
With that said I think that the PR could be merged as it is now. Those optimizations could be e.g. implemented later at some point since the purpose of this PR is fixing the bug, not optimizing the code. If you decided not to implement them now, could you write a short note or TODO describing that this part can be optimized? Btw, there was a note about optimization in the deleted section of the code, a different one but related.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-06-18 12:06:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/311#issuecomment-113130894  

I'm ok with merging.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-06-18 12:55:24 UTC  
> Updated_at: 2015-06-19 06:38:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/311#discussion_r32725935  

Regarding the part where the point's latitude is in `[lat_min, lat_max]`, your suggestion will not work in all cases. The spherical projection of the point in the box's segment may not be inside the segment, even though the latitude of the point is inside the segment's latitude range. So in general it will not work.  
  
Regarding the other suggestion to first find the **_closest meridian**_, I agree that it can be done, and thus we can avoid one strategy call.  
Good point! Thanks! I will implement it ASAP.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2015-06-18 13:03:33 UTC  
> Updated_at: 2015-06-19 06:38:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/311#discussion_r32726626  

Yes, you're right. The strategy must be called in all cases since the shortest path from point to the meridian will be a fragment of a great circle, and won't go along a parallel.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-06-18 22:20:48 UTC  
> Updated_at: 2015-06-19 06:38:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/311#discussion_r32785166  

Commit 23addc2 implements the suggested optimization.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2015-06-18 23:05:14 UTC  
> Updated_at: 2015-06-19 06:38:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/311#discussion_r32788133  

Do I understand correctly that those conditions requires that the plon must be in the valid [-pi, pi] range?  
  
I also have a small suggestion. I'm guessing that if some boolean value indicating which corner should be used was calculated at the beginning, and then after all of those conditions and setting this boolean value a strategy was called with corresponding parameters, it'd allow the compiler to optimize the code more easily. Now in each branch there is a potentially inlineable function call which could result in a code bloat and worse instruction cachng. With this boolean only this variable, most certainly optimized out would be calculated and then there would be only one if/else with function calls. So:  
  
```  
if (...) {  
    temp = ...  
    if (...)  
        use_left = true;  
    else  
        use_left = false;  
} else {  
    temp = ...  
    if (...)  
        use_left = false;  
    else  
        use_left = true;  
}  
if ( use_left )  
    m_ps_strategy.apply(point, bottom_left, top_left);  
else  
    m_ps_strategy.apply(point, bottom_right, top_right);  
```  
  
This theory could of course be verified but still, if those strategies was called in one place, at the end, the code would probably also be more clear and safe since there would be less function calls into which wrong parameters could be passed.  
  
Not to mention that it would probably be possible to calculate this boolean variable using terniary `?:` operators or at least use it for the calculation of partial results. Or maybe even to write everything in one if condition, though this would probably make the code less readable.

---

## Comment 7

> Username: mkaravel  
> Created_at: 2015-06-19 06:38:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/311#discussion_r32804567  

Done. See commit 933ed07.

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-06-21 20:55:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/311#issuecomment-113956299  

@barendgehrels Are you okay with merging this PR?

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2015-06-21 21:05:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/311#issuecomment-113958810  

Yes I'm OK with merging.

---
