# #438 Point-box and box-box distance for spherical and geographic CS [Merged]

> Username: vissarion  
> Created at: 2017-12-05 13:50:28 UTC  
> Updated at: 2018-04-12 13:10:47 UTC  
> Merged at: 2018-01-22 22:02:01 UTC  
> Closed at: 2018-01-22 22:02:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/438  

This PR adds support for point-box and box-box distance for spherical and geographic CS. It uses the meridian distance to compute distances from the horizontal line of a box and point-segment distance for distances to the vertical line.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2018-01-21 22:51:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/438#issuecomment-359288815  

Thanks! I'm ok with merging however I have 2 remarks:  
1. I'm not sure if `strategies` is a good directory for generic P/B and B/B strategies. I'd rather put them in `strategies/spherical`. Even in the same files as spherical P/B and B/B strategies, in the `strategy::distance::details` namespace to hide it from the users.  
2. Using this opportunity I think we should rename distance strategies in a consistent way. Throwing `cross_track` part would be a good start. Then we'd have e.g.: `strategy::distance::geographic_box_box`. But we should take into account all geometries combinations and design consistent scheme. I'm ok with doing it in a different PR.

---

## Comment 2

> Username: vissarion  
> Created_at: 2018-01-22 14:56:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/438#issuecomment-359447412  

> 1. I'm not sure if strategies is a good directory for generic P/B and B/B strategies. I'd rather put them in strategies/spherical. Even in the same files as spherical P/B and B/B strategies, in the strategy::distance::details namespace to hide it from the users.  
  
I did the change following your suggestion. I am ok for now but in the future it is not very clear to have generic algorithms (i.e. for both geographic and spherical) hidden in spherical or at least be consistent in similar cases.     
  
> 2. Using this opportunity I think we should rename distance strategies in a consistent way. Throwing cross_track part would be a good start. Then we'd have e.g.: strategy::distance::geographic_box_box. But we should take into account all geometries combinations and design consistent scheme. I'm ok with doing it in a different PR.  
  
I agree, there are many issues with the naming of distance strategies. Maybe better in a different PR.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2018-01-22 22:01:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/438#issuecomment-359582464  

Thanks.  
  
I noticed that you commented out `#include <boost/geometry/core/srs.hpp>` in test file which was included for `sphere` or `spheroid`. FYI, projections PR (https://github.com/boostorg/geometry/pull/394) moved them from `geometry/core` directory to `geometry/srs` directory. `geometry/srs/srs.hpp` includes both models but also projections and transformations. So if you only need `sphere` or `spheroid` it's better to include `<boost/geometry/srs/sphere.hpp>` or `<boost/geometry/srs/spheroid.hpp>` respectively. But if the code works as it is right now (without the includes) it probably means that these models are included in somewhere else, probably in some strategy files.  
  
So I'll merge this PR now and if needed tweak the includes afterwards.

---

## Comment 4

> Username: vissarion  
> Created_at: 2018-01-23 10:36:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/438#issuecomment-359749446  

>I noticed that you commented out #include <boost/geometry/core/srs.hpp> in test file which was included for sphere or spheroid. FYI, projections PR (#394) moved them from geometry/core directory to geometry/srs directory. geometry/srs/srs.hpp includes both models but also projections and transformations. So if you only need sphere or spheroid it's better to include <boost/geometry/srs/sphere.hpp> or <boost/geometry/srs/spheroid.hpp> respectively. But if the code works as it is right now (without the includes) it probably means that these models are included in somewhere else, probably in some strategy files.  
  
The code compiles as it is, that's why I commented out. Thanks.

---
