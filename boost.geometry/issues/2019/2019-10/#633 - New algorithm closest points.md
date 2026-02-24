# #633 - New algorithm closest points [Closed]

> Username: vissarion  
> Created at: 2019-10-31 10:38:25 UTC  
> Updated at: 2022-10-19 13:22:39 UTC  
> Closed at: 2022-10-19 13:22:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/633  

I am working on a new algorithm (`closest_points`) that given any two geometries `A`, `B` computes the two closest points, `a`, `b` such that `a` belongs to `A` and `b` belongs to `B`. In other words the distance from `a` to `b` equals the distance from `A` to `B`. Here is the working branch https://github.com/vissarion/geometry/tree/feature/shortest_points_new  
  
There are cases of pairs of geometries where `a` and `b` are not unique. *E.g.* two spherical or geographic boxes with overlapping longitude bands, two cartesian geometries with parallel segments, a linestring and a point where more than one segment obtain the minimum distance to a point, etc.   
  
Hence, in general the closest set is a set of point pairs and/or segment pairs. This could be modelled as a pair of multipoint and a multilinestring. That is, this pair will be the output of the `closest_points` algorithm. That said maybe the name `closest_points` is a bit misleading.   
  
Another question here is what happens when the distance is `0`. One simple solution could be to also return the distance and in the case of distance `0` the closest set has no meaning.  
  
What do you think? Any ideas/objections?

---

## Comment 1

> Username: awulkiew  
> Created at: 2019-10-31 17:10:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/633#issuecomment-548476556  

I would not worry about the uniqueness. I think we should return the first found closest pair without any additional guarantees. This will allow us to implement the algorithm of the smallest complexity which works the same as `distance` and `comparable_distance` so we'll be able to reuse the code for all algorithms.  
  
Regarding returning the distance together with closest points I think I'd prefer not doing that. Internally the algorithm should probably be based on comparable distances. And the intention of the user may be to calculate comparable distance (not real distance). So this would add unnecessary overhead.  
  
I also wouldn't worry about a user calling either `distance` or `comparable_distance` later for the points returned from `closest_points` because it has the most sense to call this algorithm for complex geometries which requires to call `comparable_distance` multiple times internally. One more call doesn't add much. This of course depends on the size of geometries.  
  
Regarding the distance `0`. I propose to also return a pair of points. It is possible to do this without additional computation but it would require to add an algorithm/utility internally replacing `intersects`/`disjoint`. Basically `intersects` returning a point related to the intersection somehow. In `intersects` internally `get_turns` is called which finds an intersection point and then if it is not found `within`/`point_in_geometry` is called for both geometries in order to check if one geometry is within the other. The information about the closest points being either first found intersection point or one of the points used to test for `within` is there.  
  
So I suggest to simply return any pair of closest points that was found ASAP.

---

## Comment 2

> Username: adityamohan29  
> Created at: 2020-03-14 15:30:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/633#issuecomment-599080220  

Hi, Has a PR been submitted for this issue yet or does @vissarion want someone else to work on this? Else we can try a divide-and-conquer approach for the same (which proves to be quite efficient, as I have implemented it before).  
https://en.wikipedia.org/wiki/Dynamic_convex_hull

---

## Comment 3

> Username: sudo-panda  
> Created at: 2020-03-14 15:46:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/633#issuecomment-599083435  

@spacewafer vissarion has been working on this for quite a while as you can see it in his [fork](https://github.com/vissarion/geometry/tree/feature/shortest_points_new)

---

## Comment 4

> Username: adityamohan29  
> Created at: 2020-03-14 15:49:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/633#issuecomment-599083973  

@sudo-panda  Oh alright then, Thanks!

---

## Comment 5

> Username: vissarion  
> Created at: 2022-10-19 13:22:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/633#issuecomment-1284008854  

Implemented in https://github.com/boostorg/geometry/pull/939 https://github.com/boostorg/geometry/pull/923
