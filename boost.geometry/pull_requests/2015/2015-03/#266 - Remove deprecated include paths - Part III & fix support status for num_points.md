# #266 Remove deprecated include paths - Part III & fix support status for num_points [Merged]

> Username: mkaravel  
> Created at: 2015-03-12 09:06:35 UTC  
> Updated at: 2015-03-12 14:44:27 UTC  
> Merged at: 2015-03-12 12:51:32 UTC  
> Closed at: 2015-03-12 12:51:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/266  

This PR is about two issues:  
- Removal of deprecated includes referring to the `include/boost/geometry/multi/` directory  
- Fixes the support status call for `num_points`: `dispatch::num_points<>` takes a second boolean argument  
  with no default value, which was not accounted for by support status.  
  
This PR may be merged independently or other PRs I have created.

---
