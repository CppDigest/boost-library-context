# #61 [voronoi] Avoiding lossy circle event position calculation... [Closed]

> Username: eadf  
> Created at: 2021-03-21 13:14:49 UTC  
> Updated at: 2023-10-28 16:47:01 UTC  
> Closed at: 2023-10-28 16:47:01 UTC  
> Url: https://github.com/boostorg/polygon/pull/61  

...when exact position is already known.  
  
It seems like when:   
point_index==1 the c_event ~is~ may be at site1.point0 == site2.point0 == site3.point1  
point_index==2 the c_event ~is~ may be at site1.point0 == site2.point1 == site3.point0  
  
But I'm not confident that's always the case.   
  
This could also be an opportunity to flag the circle event as a 'site-vertex'.   
i.e. a circle event where the position coincides with a site event.   
  
Edit: forgot to mention that this will only be true for connected segments, aka polylines. If the input data only consist of disconnected segments the `(site1.point0() == site2.point0()) || ....` test will always return false.

---

## Comment 1

> Username: eadf  
> Created_at: 2023-10-28 16:47:01 UTC  
> Url: https://github.com/boostorg/polygon/pull/61#issuecomment-1783867672  

I have indications that this PR might be incorrect, and besides: this was so long ago that i can't support this PR anymore

---
