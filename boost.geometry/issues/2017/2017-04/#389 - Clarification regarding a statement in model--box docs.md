# #389 - Clarification regarding a statement in model::box docs [Closed]

> Username: agauniyal  
> Created at: 2017-04-02 16:14:31 UTC  
> Updated at: 2017-04-03 06:02:02 UTC  
> Closed at: 2017-04-03 06:02:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/389  

I was reading the documentation here - http://www.boost.org/doc/libs/1_63_0/libs/geometry/doc/html/geometry/reference/models/model_box.html#geometry.reference.models.model_box.c1  
  
and it mentions that  
> Box is always described by a min_corner() and a max_corner() point. If another rectangle is used, use linear_ring or polygon.  
  
What is meant by - '_if another rectangle is used_' here? Does it means more than 2 points are provided or something else that I'm missing?  
  
And what exactly is meant by `min_corner()` and `max_corner()`? Does it means more inclination to negative side and positive side? What happens if I reverse them while constructing?

---

## Comment 1

> Username: awulkiew  
> Created at: 2017-04-02 23:13:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/389#issuecomment-291022048  

Min/max corner means that a box is represented by exactly 2 points, the one with lower coordinates and the one with greater coordinates, e.g. `min=(0, 1)` and `max=(10, 7)`. Rings (polygons without holes) and Polygons (polygons with holes) can also represent 2d Boxes, i.e. rectangles but in this case they need 4 or 5 points (open v.s. closed), e.g. in WKT `POLYGON((0 1, 0 7, 10 7, 10 1, 0 1))`.  
  
There are more differences between them:  
- The purpose of Boxes and Rings/Polygons is different  
- Boxes are used in spatial indexing/space partitioning as simplified representation of any geometry  
- Rings/Polygons are much more complex  
- relational operations for Boxes are very simple and in some edge cases may give different results than relational operations for Polygons  
- in non-cartesian coordinate systems Boxes don't correspond to Polygons with the same corners because segments of a non-cartesian Polygon are different, they're defined by geodesics.

---

## Comment 2

> Username: agauniyal  
> Created at: 2017-04-03 06:02:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/389#issuecomment-291055413  

Thankyou for replying so quickly, my query is resolved 👍
