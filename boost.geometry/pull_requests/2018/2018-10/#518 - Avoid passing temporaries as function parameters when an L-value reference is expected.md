# #518 Avoid passing temporaries as function parameters when an L-value reference is expected [Merged]

> Username: xventura81  
> Created at: 2018-10-18 10:05:51 UTC  
> Updated at: 2018-10-19 11:12:14 UTC  
> Merged at: 2018-10-19 11:12:04 UTC  
> Closed at: 2018-10-19 11:12:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/518  

Solve problem described in #517

---

## Comment 1

> Username: awulkiew  
> Created_at: 2018-10-18 10:18:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/518#issuecomment-430955439  

Boost.Geometry supports C++03 so your proposal has to do it as well.

---

## Review 2 [Commented]

> Username: mloskot  
> Created_at: 2018-10-18 17:34:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/518#pullrequestreview-166212385  

📁 include/boost/geometry/algorithms/append.hpp

```diff
 102 |             append_point<ring_type, Point>::apply(
 102 |-                         ext_ring, point);
 103 |+ 				ext_ring, point);
```

> Username: mloskot  
> Created_at: 2018-10-18 17:34:47 UTC  
> Updated_at: 2018-10-19 11:08:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/518#discussion_r226398383  

@xventura81 Has your editor changed spaces vs tabs? Not sure what happened here.

> Username: xventura81  
> Created_at: 2018-10-18 18:19:23 UTC  
> Updated_at: 2018-10-19 11:08:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/518#discussion_r226414237  

Yeah, Visual Studio does it by default.

> Username: mloskot  
> Created_at: 2018-10-18 18:56:57 UTC  
> Updated_at: 2018-10-19 11:08:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/518#discussion_r226426542  

IMHO, that is not good to let accidental style changes happen. Every editor can be controlled to prevent it from replacing spaces with tabs or auto-reformatting. Imagine N authors working on common code base use N editors and each editor has different auto-formatting style as default... :-)

> Username: xventura81  
> Created_at: 2018-10-18 19:21:07 UTC  
> Updated_at: 2018-10-19 11:08:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/518#discussion_r226433715  

You are right. Let's revert those changes in the indentation.


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2018-10-18 21:00:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/518#pullrequestreview-166293729  

📁 include/boost/geometry/algorithms/append.hpp

```diff
  99 |         if (ring_index == -1)
 100 |         {
 101 |+ 			ring_type& ext_ring = exterior_ring(polygon);
```

> Username: awulkiew  
> Created_at: 2018-10-18 21:00:47 UTC  
> Updated_at: 2018-10-19 11:08:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/518#discussion_r226463978  

`exterior_ring()` may return not-real reference so you should use `ring_return_type<Polygon>::type` here, similar to `interior_return_type<Polygon>::type` you used. Otherwise you may end up with dangling reference,

> Username: xventura81  
> Created_at: 2018-10-19 07:25:36 UTC  
> Updated_at: 2018-10-19 11:08:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/518#discussion_r226554235  

Thank you for this observation.


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2018-10-18 21:01:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/518#pullrequestreview-166293898  

📁 include/boost/geometry/algorithms/append.hpp

```diff
 124 |         if (ring_index == -1)
 125 |         {
 126 |+ 			ring_type& ext_ring = exterior_ring(polygon);
```

> Username: awulkiew  
> Created_at: 2018-10-18 21:01:14 UTC  
> Updated_at: 2018-10-19 11:08:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/518#discussion_r226464111  

`ring_return_type<Polygon>::type`


---

## Comment 5

> Username: awulkiew  
> Created_at: 2018-10-18 21:05:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/518#issuecomment-431162998  

I made some comments but they are outdated now. Please check them out. I won't create new ones to avoid spamming.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2018-10-19 10:39:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/518#issuecomment-431321412  

Thanks! Now I'd only suggest to squash all of the commits into one. What do you think?  
  
Something like:  
  
    git reset --soft HEAD~4  
    git commit  
    git push -f

---

## Comment 7

> Username: xventura81  
> Created_at: 2018-10-19 11:09:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/518#issuecomment-431328462  

> Thanks! Now I'd only suggest to squash all of the commits into one. What do you think?  
>   
> Something like:  
>   
> ```  
> git reset --soft HEAD~4  
> git commit  
> git push -f  
> ```  
  
Done! Thank for all the observations.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2018-10-19 11:11:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/518#issuecomment-431328879  

Great, thanks!

---
