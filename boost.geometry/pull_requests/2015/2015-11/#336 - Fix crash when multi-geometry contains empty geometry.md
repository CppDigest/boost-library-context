# #336 [algorithms][centroid] Fix crash when multi-geometry contains empty geometry [Merged]

> Username: talaj  
> Created at: 2015-11-11 21:33:39 UTC  
> Updated at: 2015-11-26 13:33:22 UTC  
> Merged at: 2015-11-22 13:15:33 UTC  
> Closed at: 2015-11-22 13:15:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/336  

Fixes seg-faulting of centroid algorithm on multi-geometries when sub-geometry is empty. Test case is included.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-11-16 12:34:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/336#issuecomment-157014780  

Thanks!  
  
It's a case when an element of multi-geometry is invalid/empty. Typically centroid() throws for empty input (if BOOST_GEOMETRY_CENTROID_NO_THROW is not defined), though in this case only a part of an input geometry is "empty". Strictly speaking the multi-geometry is invalid but of course for invalid input a fuction shouldn't crash. So such empty multi-geometry elements would be simply ignored. I'm wondering, maybe the function should throw an exception also in this case?

---

## Comment 2

> Username: talaj  
> Created_at: 2015-11-16 12:50:37 UTC  
> Updated_at: 2015-11-16 13:10:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/336#issuecomment-157018366  

@awulkiew - I think the centroid algorithm should not throw on empty sub-geometries, because if we understand centroid as synonym for [center of mass](https://github.com/boostorg/geometry/blob/1500dc364452c4219e00b6d241cfe71362b88b33/include/boost/geometry/algorithms/centroid.hpp#L586) it makes sense to just ignore empty parts as it doesn't add any weight to the calculation.  
  
It's also different case than completely empty input. There is no sensible definition of centroid for completely empty geometry.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-11-18 08:18:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/336#issuecomment-157641393  

Suppose we have a polygon that has a valid exterior ring and an empty interior ring; the polygon is obviously invalid, but we can really compute its centroid.  
  
What I am trying to say here is that the prevailing approach of BG with respect to invalid geometries is of course not to crash, but also compute something as meaningful as possible. Following this "rule" I think that, in principle, when computing the centroid of a geometry with empty sub-geometries, the empty sub-geometries should be ignored.  
  
However, let's look the above "rule" in more detail. Below I am considering the possible cases with respect to empty sub-geometries, and state my proposal regarding how we should treat them (please note that all geometries below might be invalid, but can still come up as input in centroid):  
- If the number of points of a geometry is 0, an exception should be thrown (for example when we have `MULTILINESTRING((),())').  
- If a multi-linestring with at least one point has one or more empty linestrings, ignore them.  
- If a polygon has an empty exterior ring, throw an exception (I think this is the current behavior actually).  
- If a polygon has a non-empty exterior ring and at least one empty interior ring, ignore the interior ring(s).  
- If a multi-polygon has one or more empty polygons (i.e., polygons with WKT: `POLYGON(())`) then the polygon(s) should be ignored.  
- For all other cases, the behavior of the centroid algorithm on multi-polygons really depends on the behavior of the centroid algorithm on the individual polygons contained in the multi-polygon (please correct me if I am wrong on that...).

---

## Comment 4

> Username: talaj  
> Created_at: 2015-11-18 22:11:15 UTC  
> Updated_at: 2015-11-19 10:42:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/336#issuecomment-157881064  

@mkaravel - I have added commit with tests covering your list. Only one test case is not passing - multi-polygon with empty exterior ring. Such geometry should throw, but does not currently. Single polygon with empty exterior is throwing as expected.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2015-11-19 19:00:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/336#issuecomment-158157859  

@talaj Yes, that's because the emptiness is checked on a higher level. I guess we could change it and therefore check conditions deeper in the algorithm. Though I'm ok with merging the PR as it is.

---

## Comment 6

> Username: talaj  
> Created_at: 2015-11-19 19:18:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/336#issuecomment-158163017  

It's questionable whether polygon with empty exterior ring should throw because is empty or because is invalid.  
  
Should following multi-polygon throw?  
  
```  
MULTIPOLYGON(((4 1,4 3,8 3,8 1,4 1)), ((), (0 0, 1 0, 1 1, 0 1, 0 0)))  
```

---

## Comment 7

> Username: mkaravel  
> Created_at: 2015-11-19 19:59:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/336#issuecomment-158178813  

@talaj According to the rules I wrote it should throw (and this was the intention). I think the exception to be thrown is the `centroid_exception` (which does not really specify anything else other than the fact that the centroid algorithm cannot compute the centroid).

---

## Comment 8

> Username: awulkiew  
> Created_at: 2015-11-19 21:07:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/336#issuecomment-158198378  

@mkaravel And "cannot compute the centroid" means that nothing was assigned to the output. I'm wondering, are there any reasons why doesn't this function return `bool` indicating if the result was calculated correctly?

---

## Comment 9

> Username: mkaravel  
> Created_at: 2015-11-20 15:30:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/336#issuecomment-158432917  

@awulkiew Good question. I guess the only answer I can come up with is that it would complicate the return type for `return_centroid()` as it would have to be an `std::pair<Point, bool>` in that case. But this is just a guess.

---

## Comment 10

> Username: awulkiew  
> Created_at: 2015-11-20 15:58:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/336#issuecomment-158441269  

@mkaravel Well, `return_centroid()` could check the result and throw. The same is true for `envelope()`. I guess a design decision was made some time ago that all functions should work like this. The only exception I found is `intersection()` which does return `bool` however AFAIU it always returns `true` for combinations of geometries other than Box/Box. Anyway, we probably shouldn't change the behavior of `centroid()` now.

---

## Comment 11

> Username: mkaravel  
> Created_at: 2015-11-20 16:32:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/336#issuecomment-158452034  

@awulkiew You mean not change the behavior with respect the return type; I agree.  
I think we should still change the behavior for multi-polygons that have polygons with empty exterior rings.

---

## Comment 12

> Username: awulkiew  
> Created_at: 2015-11-20 18:08:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/336#issuecomment-158478079  

@mkaravel Yes, I was talking about the return type. As for the empty exterior rings, I have nothing against it. My point is that it could be done as a part of this PR or later. For now this PR is a bugfix for segfult. Should the behavior change be a part of it too? @talaj would you like to do it?

---

## Comment 13

> Username: talaj  
> Created_at: 2015-11-20 22:14:27 UTC  
> Updated_at: 2015-11-23 09:55:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/336#issuecomment-158541077  

> would you like to do it?  
  
@awulkiew - Yes, I would like. I will have some time next week. I think it should be part of this pull request as tests I have added here are failing because of it.

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2015-11-21 18:19:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/336#issuecomment-158669028  

@awulkiew @talaj @mkaravel First, to be clear, please don't change the return type indeed. If it is about exterior rings, OK for me if everyone agrees.  
Second: be aware that the branch closes soon (for bugfixes only, other changes are not allowed anymore) - if this change has to be in, and it is considered as a bugfix, it has to be soon.   
Anyway, I prefer merging the bugfix first, and do any new actions in a separte PR

---

## Comment 15

> Username: barendgehrels  
> Created_at: 2015-11-21 18:20:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/336#issuecomment-158669314  

@talaj Thanks for the bugfix!

---

## Comment 16

> Username: talaj  
> Created_at: 2015-11-21 19:56:25 UTC  
> Updated_at: 2015-11-26 13:33:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/336#issuecomment-158677619  

@barendgehrels - Ok, I removed failing test case with empty exterior ring so all tests are passing now and this pull request should be ready to merge.

---

## Comment 17

> Username: barendgehrels  
> Created_at: 2015-11-21 21:12:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/336#issuecomment-158680929  

OK, thanks - everyone OK with merging?

---

## Comment 18

> Username: awulkiew  
> Created_at: 2015-11-22 13:04:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/336#issuecomment-158758596  

@barendgehrels Yes.

---

## Comment 19

> Username: mkaravel  
> Created_at: 2015-11-22 13:09:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/336#issuecomment-158758749  

@barendgehrels @talaj @awulkiew   
Yes for me as well.

---

## Comment 20

> Username: barendgehrels  
> Created_at: 2015-11-22 13:15:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/336#issuecomment-158758970  

OK, merged, thanks

---
