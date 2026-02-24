# #483 - Feature Request: Resolve Algorithm Inconsistency between segment/box and linestring/ring [Open]

> Username: e-kwsm  
> Created at: 2018-05-14 10:26:44 UTC  
> Updated at: 2024-10-02 13:05:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/483  

I tested some algorithms for binary combinations of  
- `boost::geometry::model::d2::point_xy<int>`  
- `boost::geometry::model::polygon<point_t>`  
- `boost::geometry::model::box<point_t>`  
- `boost::geometry::model::ring<point_t>`  
- `boost::geometry::model::segment<point_t>`  
- `boost::geometry::model::linestring<point_t>`  
  
as input and found inconsistencies (some are documented).  
  
The following functions do not support `segment` or `box` even though they do `linestring` and `ring`:  
- `covered_by`  
- `crosses`  
- `overlaps`  
- `relation`  
- `touches`  
  
For example `crosses(linestring, ring)` works but `crosses(segment, box)` does not.  
  
The following functions do not support only `(box, box, output_collection)`  
- `difference`  
- `intersection`  
- `sym_difference`  
- `union_`  
  
For example `union_(box, ring, output_collection)` works.  
  
If I understand correctly `segemnt` ⊂ `linestring` and `box` ⊂ `ring` ⊂ `polygon` hold (actually convertible).  
These inconsistencies are not intuitive and force users to convert `segment` and `box`.
