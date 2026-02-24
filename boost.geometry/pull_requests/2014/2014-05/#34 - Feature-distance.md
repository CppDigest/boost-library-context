# #34 Feature/distance [Merged]

> Username: mkaravel  
> Created at: 2014-05-19 13:55:26 UTC  
> Updated at: 2014-05-19 14:43:10 UTC  
> Merged at: 2014-05-19 14:43:10 UTC  
> Closed at: 2014-05-19 14:43:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/34  

1) fix bugs in the segment-box distance computation code  
2) add alternate algorithm for computing segment-box distances (used for verification in unit tests)  
3) re-factor common code (use point_to_range instead of have custom code with the same functionality)

---
