# #521 - wrong documentation description for distance (with strategy) [Closed]

> Username: agudpp  
> Created at: 2018-10-19 15:11:26 UTC  
> Updated at: 2018-12-03 19:36:15 UTC  
> Closed at: 2018-12-03 19:36:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/521  

There is a wrong description for https://www.boost.org/doc/libs/1_68_0/libs/geometry/doc/html/geometry/reference/algorithms/distance/distance_3_with_strategy.html  
  
On the description:  
>The free function area calculates the area of a geometry. using the specified strategy. Reasons to specify a strategy include: use another coordinate system for calculations; construct the strategy beforehand (e.g. with the radius of the Earth); select a strategy when there are more than one available for a calculation.  
  
Which is a copy and paste of **area (with strategy)** I think (https://www.boost.org/doc/libs/1_68_0/libs/geometry/doc/html/geometry/reference/algorithms/area/area_2_with_strategy.html)

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-12-03 19:36:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/521#issuecomment-443841123  

Fixed by https://github.com/boostorg/geometry/pull/532
