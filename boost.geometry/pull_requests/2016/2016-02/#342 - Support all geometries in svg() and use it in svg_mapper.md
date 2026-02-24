# #342 [io] Support all geometries in svg() and use it in svg_mapper. [Merged]

> Username: awulkiew  
> Created at: 2016-02-09 13:13:58 UTC  
> Updated at: 2017-03-20 14:29:10 UTC  
> Merged at: 2016-02-09 17:52:23 UTC  
> Closed at: 2016-02-09 17:52:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/342  

SVG tag for Segment in `svg()` is `<line>`. This function is called by `svg_mapper`. The implementation of `svg_map` previously used only for Box is now used for Segment as well. Previously it was a separate implementation transforming Segments into Linestrings. This means that `svg_mapper` now outputs Segments as SVG Lines (`<line>`) instead of PolyLines (`<polyline>`).

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2016-02-09 17:32:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/342#issuecomment-181972314  

Thanks! It looks good, I am ok with merging

---
