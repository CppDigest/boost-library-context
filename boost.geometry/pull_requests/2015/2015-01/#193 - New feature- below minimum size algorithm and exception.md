# #193 New feature: below minimum size algorithm and exception [Closed]

> Username: mkaravel  
> Created at: 2015-01-13 15:23:20 UTC  
> Updated at: 2015-05-27 06:25:07 UTC  
> Closed at: 2015-05-27 06:25:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/193  

In this PR I propose a new algorithm `is_below_minimum_size` which tests whether the size of a geometry is below the minimum acceptable size so that the geometry can be considered as valid. More specifically:  
- For points, segments and boxes the algorithm always returns false.  
- For linestrings the algorithm returns true is the linestrings has less than 2 points.  
- For rings and polygons the algorithm returns true is the ring or the polygon's rings have less than 3 or 4 points, depending on whether the ring or polygon is open or closed, respectively.  
- For multi-points the algorithm returns true is the multi-point has size 0.  
- For multi-linestrings and multi-polygons the algorithm returns true if at least one of the contained geometries has less than the minimum acceptable number of points.  
  
Besides this new algorithm this PR introduces  
- a new exception called `below_minimum_size` that is to be thrown when a geometry's size is below the minimum acceptable one (in the sense described below)  
- a new free function (in `algorithms/detail`) called `throw_below_minimum_size` that takes as input a geometry and throws the `below_minimum_size` exception if the result of the `is_below_minimum_size` for this geometry is false.  
  
The motivation for this PR is to replace the `empty_geometry_exception` in the distance code, and possibly in other algorithms.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-01-13 16:58:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/193#issuecomment-69778119  

One of the main reasons for this PR was to use it in the `distance()` implementation to avoid some assertion failures (and also segfault) for an invalid input data. But if `BOOST_GEOMETRY_BELOW_MINIMUM_SIZE_NO_THROW` was defined the exception wouldn't be thrown. This means that if the `distance()` implementation used `throw_below_minimum_size()` function, and the user disabled throwing of `below_minimum_size_exception`, the assertion would still fail or there would be a segfault. So introducing this exception only partially solves the problem.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-01-14 14:00:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/193#issuecomment-69919250  

As written on the list please don't merge this.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-01-22 16:30:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/193#issuecomment-71049371  

@awulkiew I more or less agree with your argument. Notice though that even now if you define the macro `BOOST_GEOMETRY_EMPTY_INPUT_NO_THROW` it is possible to reach assertion failures in the distance code, even in cases where you would expect things to work....

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-01-22 16:33:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/193#issuecomment-71049758  

I do understand the argument to avoid throwing exceptions unless it is completely necessary.  
Regarding this PR I see two options:  
- Simply close it.  
- Keep it, go through review, etc. and use the new algorithm inside `bg::is_valid` (it can replace some code and check for the minimum size of a geometry in a more structured way).  
  
Please let me know what you think.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2015-01-22 19:06:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/193#issuecomment-71078379  

W.r.t. is_valid, what would be the difference with the existing minimum_ring_size? In most algorithms this was basically enough, so if you think this file adds something there, please explain that in a little more detail.  
  
Anyway, please don't throw it away completely, the implementation looks OK, maybe we find a usage here or for a similar purpose.

---
