# #190 Fix/centroid [Merged]

> Username: awulkiew  
> Created at: 2015-01-05 23:49:03 UTC  
> Updated at: 2015-01-10 11:41:37 UTC  
> Merged at: 2015-01-10 11:41:32 UTC  
> Closed at: 2015-01-10 11:41:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/190  

Various improvements for centroid():  
- more deterministic result for invalid geometries, this "fixes" the issue introduced by https://github.com/boostorg/geometry/pull/155, the modification (translation) of a result even if it wasn't correctly calculated,  
- check for `count == 0` in average strategy and returning `false` in this case, this also makes it CentroidStrategyConcept-conformant,  
- replacement of `int` with `std::size_t` for counters (should really be `range_size<>::type` but still safer than `int`),  
- use of default template parameters to improve readability.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-01-10 10:47:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/190#issuecomment-69451689  

Thanks Adam. I'm OK with merging it.

---
