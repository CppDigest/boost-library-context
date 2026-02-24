# #206 Fix/warnings [Merged]

> Username: awulkiew  
> Created at: 2015-02-02 18:50:38 UTC  
> Updated at: 2015-02-09 16:10:56 UTC  
> Merged at: 2015-02-09 16:10:56 UTC  
> Closed at: 2015-02-09 16:10:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/206  

This PR fixes integral conversion warnings by avoiding conversions or doing them explicitly.  
It also adds a utility - `range::pos()` function which returns an iterator on ith position of a Range.  
  
EDIT: unused parameter warnings are also fixed.

---
