# #215 Fix MSVC error regarding undiscovered function equals_point_point [Merged]

> Username: mkaravel  
> Created at: 2015-02-11 07:37:28 UTC  
> Updated at: 2015-02-11 13:07:08 UTC  
> Merged at: 2015-02-11 12:54:04 UTC  
> Closed at: 2015-02-11 12:54:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/215  

MSVC thinks that that the `detail::equals` namespace qualifying the call to `equals_point_point` is a nested namespace inside `boost::geometry::strategy::side`.  
  
Fix: qualify the call to `equals_point_point` by `geometry::detail::equals`.

---
