# #225 Fix/cart intersect [Merged]

> Username: awulkiew  
> Created at: 2015-02-13 21:14:56 UTC  
> Updated at: 2015-03-11 02:53:03 UTC  
> Merged at: 2015-02-19 23:53:51 UTC  
> Closed at: 2015-02-19 23:53:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/225  

This PR increases the robustness of the code calculating the intersection of 2 collinear segments. Instead of the ratios which depend on lendth of the segments the coordinates are compared using operator< in addition to the checks for equality which was recently added. The relative position of a point and a segment is mapped into integer range [0, 4] like this:  
  
```  
// S1pt   0  1     2    3  4  
// S2        |---------->  
```  
  
The integer values correspond to a ratio values ranges `(-oo, 0)` `{0}` `(0, 1)` `{1}` and `(1, +oo)` respectively. So e.g. the less compare works as expected for those int values, e.g. a value indicating left is still lesser than a value indicating middle and right. The data required to calculate them should be the segments' endpoints projected into 1-dimensional space, e.g. in the case of cartesian CS it's a more significant axis (X or Y), so exactly the same as with ratios.  
  
Without this change in some cases the results of direction and intersection policy are not consistent, and varying depending on the order of the segments. This behavior can be observed e.g. if one of the segments is a lot longer than the second one, e.g.:  
  
```  
"LINESTRING(-2305843009213693956 4611686018427387906, -33 -92, 78 83)"  
"LINESTRING(31 -97, -46 57, -20 -4)"  
```  
  
It's not enough to use the numerators and denominators contained in ratios because they are calculated by subtraction of coordinates, so for a very big difference of coordinates the error is also very big. And the result strongly depends on the coordinates being subtracted. In cases like the above at the same time logically excluding conditions can be met, e.g. segment detected in the middle of the other one and intersecting the endpoint. Then e.g. for segments detected as overlapping only 1 intersection point is generated (but there should be 2) which later in get turns causes an assertion failure. Note that the same effect should be observable if one of the segment had "normal" length and the other one was very short.  
  
Even with this change the "invalid" ratios are stored in the intersection result and used e.g. in the code sorting turns. We could think about storing those position values with the ratios or think about some better representation of a position/distance of a point on a segment.

---
