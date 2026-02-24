# #29 Feature/distance [Merged]

> Username: mkaravel  
> Created at: 2014-05-14 15:02:24 UTC  
> Updated at: 2014-05-14 15:04:10 UTC  
> Merged at: 2014-05-14 15:04:10 UTC  
> Closed at: 2014-05-14 15:04:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/29  

- eliminate functions make_segment, make_box2d and make_box3d  
- use wkt description for boxes and segments in distance unit tests  
- call sqrt with an explicit double argument (VS2012 complains otherwise)

---
