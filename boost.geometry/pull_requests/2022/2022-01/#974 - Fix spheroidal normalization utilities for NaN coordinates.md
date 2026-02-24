# #974 Fix spheroidal normalization utilities for NaN coordinates [Merged]

> Username: awulkiew  
> Created at: 2022-01-27 16:30:18 UTC  
> Updated at: 2022-02-08 16:32:51 UTC  
> Merged at: 2022-02-08 16:32:50 UTC  
> Closed at: 2022-02-08 16:32:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/974  

This PR fixes:  
- assertion failure in `normalize_spheroidal_coordinates` for NaN coordinates mistaken for incorrect result  
- `is_band` calculation in `normalize_spheroidal_box_coordinates` incorrectly considering a box containing NaN coordinates to go around the globe.

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2022-02-02 10:37:45 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/974#pullrequestreview-870355194  

Looks good to me, thanks

---
