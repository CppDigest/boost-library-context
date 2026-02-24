# #877 Support DG and GC in convex_hull, correct, correct_closure, is_convex, is_empty and reverse. [Merged]

> Username: awulkiew  
> Created at: 2021-06-30 21:52:32 UTC  
> Updated at: 2021-09-22 22:11:14 UTC  
> Merged at: 2021-09-22 22:11:14 UTC  
> Closed at: 2021-09-22 22:11:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/877  

In addition to the input GC and DG in convex_hull() also the output GC and DG is supported. Based on `traits::geometry_types` output type is chosen and then either emplaced back to GC or assigned to DG.  
  
In case of GC and DG the output is always valid. Degenerated rings are not returned. Instead if convex hull is a segment then this segment is returned in a linear geometry and if the hull is a point then this is returned in pointlike geometry. Any geometry can be used for that with simpler ones chosen first. E.g. if GC can store rings, polygons, linestrings, segments and points then the output will either be ring, segment or point.  
  
convex_hull is implemented in such a way that passes the GC into the graham_andrew algorithm and the algorithm works on the GC directly. In this case the convex hulls of all boxes are calculated first and passed together with the GC. The reason behind this is that boxes are treated separately.  
  
This PR addresses https://github.com/boostorg/geometry/issues/829  
  
-----  
  
In the future we should probably implement strategies for boxes for all coordinate systems.  
  
Note that calculating of hull for more than one box or a box and polygon in spherical/geographic will not be straightforward because AFAIU the problem can't be divided into calculating hulls for each of the geometries and then calculating the final hull from sub-hulls. Consider this case of box and polygon (blue) their hulls (red) and the hull of both (green).  
  
![rect833-6](https://user-images.githubusercontent.com/1226951/124038499-82d8c980-da01-11eb-9d57-ed97dc55a826.png)

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2021-07-21 11:19:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/877#pullrequestreview-711539322  

📁 include/boost/geometry/algorithms/detail/convex_hull/interface.hpp

```diff
  57 |- template <order_selector Order, closure_selector Closure>
  58 |- struct hull_insert
  69 |+ // Abstraction representing ranges/rings of a geometry
```

> Username: barendgehrels  
> Created_at: 2021-07-21 11:19:05 UTC  
> Updated_at: 2021-07-21 11:19:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/877#discussion_r673884475  

@awulkiew  can you explain the rationale of the `proxy` in more detail?  
I glanced through the code but don't get it by that yet.  
  
For example, is it just useful (shorter), or is it essential (we cannot make this improvement without). And why is it necessary for this function `convex_hull` but not (at least not yet) for other algorithms?

> Username: awulkiew  
> Created_at: 2021-07-21 14:28:06 UTC  
> Updated_at: 2021-07-21 14:28:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/877#discussion_r674025225  

Short answer:  
It's just shorter. It allows to support boxes without rewriting the `graham_andrew` algorithm or calling it twice.  
  
Long answer:  
GeometryCollection can store various kinds of geometries, e.g. Rings together with Boxes. We handle Boxes differently in a separate dispatch and generate a ring out of it. So now we have several options:  
1. pass GC into `graham_andrew` algorithm and rewrite it to distinguish between various kinds of geometries  
2. leave dispatches and `graham_andrew` as they are now and for GC separately calculate a hull for each Box, then the hull for the rest of the GC and then the resulting hull from all of the intermediate hulls  
3. leave dispatches and `graham_andrew` as they are now and for GC separately calculate a hull for each Box, but then pass them together with GC into `graham_andrews` transparently, as if the Box hulls were contained in GC instead of Boxes (I've choosen this solution)  
  
As I mentioned in the description this will work only in cartesian. In spherical/geographic we'll have to distinguish between geometries inside the algorithm. But the algorithm will probably be different than `graham_andrews`.

> Username: barendgehrels  
> Created_at: 2021-07-21 17:07:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/877#discussion_r674175950  

Thanks! I'll finish my review next week (assuming there is no hurry)

> Username: awulkiew  
> Created_at: 2021-07-21 17:55:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/877#discussion_r674208311  

No, take your time, thanks.

> Username: barendgehrels  
> Created_at: 2021-09-22 08:29:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/877#discussion_r713713018  

Oops, this was forgotten. But I'm OK with merging.


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2021-09-21 10:24:49 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/877#pullrequestreview-759566863  

Thanks, I am OK with merging! It would be useful to include some basic test cases for the new functionalities such as some convex hulls of geometry collections.

---

## Comment 3

> Username: vissarion  
> Created_at: 2021-09-21 10:29:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/877#issuecomment-923848515  

>Note that calculating of hull for more than one box or a box and polygon in spherical/geographic will not be straightforward because AFAIU the problem can't be divided into calculating hulls for each of the geometries and then calculating the final hull from sub-hulls. Consider this case of box and polygon (blue) their hulls (red) and the hull of both (green).  
  
Just a side note here, the (convex) hull of hulls should always be the hull of the original geometries (due to convexity). That is, in the picture above the red shape is not the convex hull of a box while the green is the convex hull of their union. The issue here is that we cannot represent the convex hull of a box in non-cartesian system. We can discuss this in a separate issue I guess.

---
