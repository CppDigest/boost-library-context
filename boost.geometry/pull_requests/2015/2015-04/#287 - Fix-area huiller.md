# #287 Fix/area huiller [Merged]

> Username: awulkiew  
> Created at: 2015-04-29 01:29:21 UTC  
> Updated at: 2015-06-11 16:26:16 UTC  
> Merged at: 2015-06-03 07:27:05 UTC  
> Closed at: 2015-06-03 07:27:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/287  

Inspired by: http://stackoverflow.com/questions/9439644/bug-in-boostgeometrys-area-huiller-algorithm

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-04-29 05:48:21 UTC  
> Updated_at: 2015-04-29 14:32:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/287#discussion_r29311763  

I would keep these three lines of comments I think they are very useful.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-04-29 05:53:18 UTC  
> Updated_at: 2015-04-29 14:32:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/287#discussion_r29311883  

Although it does not make much of a difference, I would write this as `lon < 0`. If `lon` is 0 then the segment line along a meridian and the area of the triangle formed by `p1`, `p2` and the south pole is identically equal to zero.  
  
My suggestions would be:  
1. Check if the two longitudes are the same. Then in this case do not compute the excess, but rather set it directly to 0.  
2. Use `math::smaller(lon, 0)` rather than `lon < 0`.  
3. Use a different name for the variable `lon`; it refers to the difference of longitudes and the current name is somehow misleading.  
4. Follow our coding rules and do not put spaces after `(` and before `)`.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-04-29 05:53:48 UTC  
> Updated_at: 2015-04-29 14:32:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/287#discussion_r29311896  

According to our coding conventions this should read `if (lon > pi)`.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-04-29 06:07:22 UTC  
> Updated_at: 2015-04-29 14:32:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/287#discussion_r29312215  

You do not need to create a local variable for π. It is used only once.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-04-29 06:23:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/287#issuecomment-97320709  

Adam, thanks for going over this strategy.  
  
AFAIU there is also a problem when one of the vertices of the areal geometry is the south pole. Based on some experiments of my own, it seems that if the south pole is a vertex of the ring/polygon/multipolygon, then the `area()` method of the strategy should return `sum * radius * radius` rather than `-sum * radius * radius`; consider for example the clockwise closed ring (in degrees) `POLYGON((50 0,100 0,100 -90,50 0))`, which gives me a negative area, whereas it should clearly have been positive. The way to fix this is to keep track of whether the south pole is a vertex of the geometry considered and properly return what should be returned.

---

## Comment 6

> Username: mkaravel  
> Created_at: 2015-04-29 06:51:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/287#issuecomment-97327101  

There are at least two more issues with our implementation of this strategy:  
1. It does not handle correctly areal geometries that have segments that contain the south pole in their interior (this could be due to numerical errors, but still....).  
2. It implicitly assumes that the coordinates of the points passed are normalized. If you pass out-of-range longitudes for example, it can easily fail.  
  
I already have a prototype that fixes most of these issues. I should be able to finish it soon and be able to push it.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2015-04-29 11:03:35 UTC  
> Updated_at: 2015-04-29 14:32:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/287#discussion_r29326276  

I've done it because of the convention used. Any of the above variables is not needed. And the compiler should optimize them out anyway.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2015-04-29 12:01:26 UTC  
> Updated_at: 2015-04-29 14:32:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/287#discussion_r29328979  

Yes I was considering using `lon < 0`, it gives good results. I just decided to keep the approach used in the book mainly because below `lon` is compared with `pi` so both checks must be consistent and I didn't analyse if the result should be ok. The naming is also taken from the book. In the same code there is a variable called `E` probably also taken from some source. In general I noticed that in Geometry names of variables are taken from papers even if they are against our guidelines. But sure, I already altered the algorithm so I can change the naming as well.  
  
AFAIU using `math::equals()` or `math::smaller()` could add an error because the longitudes would be compared according to the EPS. So currently a value of `E` may be calculated and it may be not fully consistent with the value of `lon` and `pi`. But do you think this is a problem? Probably only if the value of `E` can changes rapidly and the later checks are there to reverse this change, but I think that this isn't the case. I'll think about it.

---

## Comment 9

> Username: awulkiew  
> Created_at: 2015-04-29 12:05:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/287#issuecomment-97401967  

My intention was to fix the issue that is currently there for a very basic case. The problems around poles are a known issue. AFAIU this algorithm in general is not suitable for polygons around poles so for them we'd be forced to implement some workaround. But I'm not sure if it should be a part of this PR.  
  
We didn't yet agree how the normalization of coordinate should look like. Maybe we shoudn't normalize the coordinates at all?

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2015-04-29 14:09:31 UTC  
> Updated_at: 2015-04-29 14:32:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/287#discussion_r29338664  

Yes, please don't remove these kind of comments

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2015-04-29 14:13:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/287#issuecomment-97441351  

Thanks - I'm OK with merging after comments are addressed

---

## Comment 12

> Username: mkaravel  
> Created_at: 2015-04-29 14:15:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/287#issuecomment-97442457  

I am not okay with merging BTW. I will give more info soon.

---

## Comment 13

> Username: awulkiew  
> Created_at: 2015-04-29 14:28:55 UTC  
> Updated_at: 2015-04-29 14:32:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/287#discussion_r29340660  

Sorry, I got an impression that it's a comment made explicitly for the approach used below. But indeed it's more general.

---
