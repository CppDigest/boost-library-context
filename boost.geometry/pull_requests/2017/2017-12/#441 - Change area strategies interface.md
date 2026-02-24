# #441 Change area strategies interface. [Merged]

> Username: awulkiew  
> Created at: 2017-12-22 14:21:54 UTC  
> Updated at: 2018-01-23 00:10:54 UTC  
> Merged at: 2018-01-22 22:05:18 UTC  
> Closed at: 2018-01-22 22:05:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/441  

The purpose of this PR is to remove the `PointOfSegment` template parameter from area strategies in order to simplify the interface. The outcome:  
  
    a1 = bg::area(poly1, bg::strategy::area::cartesian<>());  
    a2 = bg::area(poly2, bg::strategy::area::spherical<>(6371000.0));  
    a3 = bg::area(poly3, bg::strategy::area::geographic<>(bg::srs::spheroid<double>(6378137.0, 6356752.314245)));  
  
Spherical strategy may take either `Sphere` model (for compatibility with `geographic` strategy) or radius (for compatibility with `haversine` distance strategy). This is true for template parameter defining the type of radius stored in strategy and for constructor parameter. By default the radius is set to `1` for backward compatibility.  
  
List of changes:  
- Remove `PointOfSegment` template parameter from all area strategies.  
- Replace `return_type` member type with `result_type<>` member  
  template/metafunction.  
- Replace `state_type` member type with `state<>` member template.  
- Remove `segment_point_type` member type.  
- Alter area strategy concept check accordingly.  
- Rename `bg::strategy::area::surveyor` to `bg::strategy::area::cartesian`.  
- Remove `Point` template parameter from  
  `bg::strategy::area::services::default_strategy`, after that it depends only on `CSTag`.  
- Add `bg::area_result` metafunction defining result type for `Geometry` and  
  `Strategy`. Similar to `bg::default_area_result`, `bg::distance_result`, etc.  
- Support this new area strategy interface in algorithms and non-area strategies.

---

## Comment 1

> Username: vissarion  
> Created_at: 2018-01-22 15:21:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/441#issuecomment-359455663  

Thanks! I am ok with merging.

---
