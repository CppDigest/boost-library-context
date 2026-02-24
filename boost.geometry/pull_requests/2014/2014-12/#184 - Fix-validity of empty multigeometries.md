# #184 Fix/validity of empty multigeometries [Merged]

> Username: mkaravel  
> Created at: 2014-12-05 15:24:50 UTC  
> Updated at: 2015-02-03 20:02:39 UTC  
> Merged at: 2014-12-05 19:32:54 UTC  
> Closed at: 2014-12-05 19:32:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/184  

This PR changes the validity check for multi-geometries: multi-geometries with no elements are now considered as valid.  
The unit test for `bg::is_valid` is also updated, as well as the document, which now reflects this change.

---
