# #325 Fix/difference intersection linear areal [Merged]

> Username: mkaravel  
> Created at: 2015-09-03 12:35:47 UTC  
> Updated at: 2015-10-14 08:15:09 UTC  
> Merged at: 2015-10-09 05:13:52 UTC  
> Closed at: 2015-10-09 05:13:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/325  

The existing `bg::intersection(L, A)` and bg::difference(L, A)` algorithm discard spikes present in the linear geometry passed to the algorithm. This is not only inconsistent with the fact that Boost.Geometry considers linear geometries with spikes as valid, but can also produce invalid output.  
  
To give an example of one such case where the output can be invalid, consider the linestring `LINESTRING(0 5,-5 5,0 5)` and the polygon `POLYGON((0 0,0 10,10 10,10 0,0 0))`. The existing code returns as output of `bg::difference()` the linestring `LINESTRING(0 5,0 5)` which is invalid (it is 0-dimensional instead of 1-dimensional).  
  
Another interesting case is the test case where we have the linestring `LINESTRING(0 5,5 5,0 5)` and the polygon `POLYGON((0 0,0 10,10 10,10 0,0 0))`. The expected output is the empty multilinestring, but instead the linestring `LINESTRING(0 5,0 5)` is again reported.  
  
As far as spikes are concerned, this is handled by specifying the last template parameter of `bg::detail::overlay::follow<>` to be `false` instead of `true` (the default value is `true` and signifies that spikes should be discarded).  
  
As far as the second case is concerned, it is handled now by performing an initial scan of the turns computed, to determine is there are any turns that are considered as _crossing_ turns, i.e., correspond to intersection points at which the linestring leaves the closure of the areal geometry and goes towards the free space, or vice versa. If no such turns are found, then the algorithm determines if the entire linear geometry lies inside the areal geometry or not, in exactly the same way as if no turns where found.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-09-03 17:02:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/325#issuecomment-137511995  

I am still working on this PR, as there seem to be some problems with my solution.  
I will post a message once the PR is ready for review.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-09-04 10:23:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/325#issuecomment-137697625  

The PR is now ready for review.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2015-09-16 13:59:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/325#issuecomment-140750328  

Thanks! I'm of course ok with merging.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-09-16 20:55:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/325#issuecomment-140891939  

@barendgehrels What do you think?

---

## Comment 5

> Username: awulkiew  
> Created_at: 2015-10-08 18:22:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/325#issuecomment-146644712  

AFAIU this PR is ready for merging. Barend are you ok with this?

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2015-10-08 21:16:15 UTC  
> Updated_at: 2015-10-09 05:12:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/325#discussion_r41570121  

What do you mean by tab or mab? touch/blocked/...

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2015-10-08 21:17:29 UTC  
> Updated_at: 2015-10-09 05:12:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/325#discussion_r41570254  

Index

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2015-10-08 21:18:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/325#issuecomment-146688825  

I'm OK with merging

---

## Comment 9

> Username: mkaravel  
> Created_at: 2015-10-09 05:13:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/325#discussion_r41598118  

This was a name used in an earlier version of this method.  
I changed the name to something more descriptive.

---

## Comment 10

> Username: mkaravel  
> Created_at: 2015-10-09 05:13:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/325#discussion_r41598125  

Done.

---

## Comment 11

> Username: mkaravel  
> Created_at: 2015-10-09 05:14:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/325#issuecomment-146756669  

I have merged the PR. If the latest (naming) changes need further improvement please let me know.

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2015-10-09 06:47:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/325#issuecomment-146771242  

Thanks, it is OK.

---
