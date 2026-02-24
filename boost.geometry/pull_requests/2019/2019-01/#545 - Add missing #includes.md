# #545 Add missing #includes [Merged]

> Username: awulkiew  
> Created at: 2019-01-03 03:37:11 UTC  
> Updated at: 2019-01-15 13:19:03 UTC  
> Merged at: 2019-01-15 13:19:03 UTC  
> Closed at: 2019-01-15 13:19:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/545  

This PR adds missing includes in order to make headers in line with Boost one header policy.  
A part of fixes needed by https://github.com/boostorg/geometry/issues/523  
Not all headers are fixed by this PR.  
  
This PR also decrease inter-dependencies:  
- projections are no longer included by `meridian_segment` formula,  
- the whole `within`, `covered_by`, `relate` and `rtree` code is no longer included by distance strategies

---
