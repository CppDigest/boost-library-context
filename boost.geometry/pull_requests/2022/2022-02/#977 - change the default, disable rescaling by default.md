# #977 [major change] change the default, disable rescaling by default [Merged]

> Username: barendgehrels  
> Created at: 2022-02-16 18:06:52 UTC  
> Updated at: 2022-05-22 07:31:19 UTC  
> Merged at: 2022-02-23 11:49:58 UTC  
> Closed at: 2022-02-23 11:49:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/977  

This will disable rescaling by default. Current behavior on most testsuites, using `BOOST_GEOMETRY_TEST_FAILURES` (so it checks how many failures there are left) is shown below.  
  
It's in almost all cases better now then with rescaling (but it took me quite some time...)  
  
Here is the list (occasionally the expectations are not updated and off, but it's the **detected** which is important)  
  
**Set operations**, without rescaling:  
```  
===  union using double  
NOT RESCALED - Expected: 1 error(s) ** 1 failure detected  
===  union_multi using double  
NOT RESCALED - Expected: 0 error(s) ** No errors detected  
===  intersection using double  
NOT RESCALED - Expected: 2 error(s) ** 2 failures detected  
===  intersection_multi using double  
NOT RESCALED - Expected: 1 error(s) ** 1 failure detected  
===  difference using double  
NOT RESCALED - Expected: 5 error(s) ** 5 failures detected  
===  difference_multi using double  
NOT RESCALED - Expected: 11 error(s) ** 12 failures detected  
```  
  
**Set operations**, with rescaling:  
```  
===  union using double  
RESCALED - Expected: 4 error(s) ** 4 failures detected  
===  union_multi using double  
RESCALED - Expected: 9 error(s) ** 9 failures detected  
===  intersection using double  
RESCALED - Expected: 6 error(s) ** 6 failures detected  
===  intersection_multi using double  
RESCALED - Expected: 9 error(s) ** 9 failures detected  
===  difference using double  
RESCALED - Expected: 15 error(s) ** 15 failures detected  
===  difference_multi using double  
RESCALED - Expected: 24 error(s) ** 25 failures detected  
```  
  
**Buffer**, without rescaling:  
```  
=== RUNNING buffer_polygon using double  
NOT RESCALED - Expected: 1 error(s) ** 1 failure detected  
== RUNNING buffer_multi_polygon using double  
NOT RESCALED - Expected: 1 error(s) ** 1 failure detected  
== RUNNING buffer_linestring using double  
NOT RESCALED - Expected: 4 error(s) ** 4 failures detected  
== RUNNING buffer_multi_linestring using double  
NOT RESCALED - Expected: 6 error(s) ** 6 failures detected  
== RUNNING buffer_linestring_aimes using double  
NOT RESCALED - Expected: 0 error(s) ** No errors detected  
```  
  
**Buffer**, with rescaling:  
```  
== RUNNING buffer_polygon using double  
RESCALED - Expected: 2 error(s) ** 2 failures detected  
== RUNNING buffer_multi_polygon using double  
RESCALED - Expected: 3 error(s) ** 3 failures detected  
== RUNNING buffer_linestring using double  
RESCALED - Expected: 2 error(s) ** 2 failures detected  
== RUNNING buffer_multi_linestring using double  
RESCALED - Expected: 9 error(s) ** 8 failures detected  
== RUNNING buffer_linestring_aimes using double  
RESCALED - Expected: 0 error(s) ** No errors detected  
```

---

## Comment 1

> Username: awulkiew  
> Created_at: 2022-02-17 12:07:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/977#issuecomment-1042882287  

That's great!  
  
What do you think about removing robust policy from all of the functions?

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2022-02-17 15:23:11 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/977#pullrequestreview-886129487  

LGTM

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2022-02-17 18:55:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/977#issuecomment-1043302928  

> That's great!  
>   
> What do you think about removing robust policy from all of the functions?  
  
Yes, that should be done at some point. But that disables all rescaling - which we indeed want, but I think we should not do that in this release yet. In next, or using the common deprecation period (was that 3 releases)?  
  
Because this change influences behavior, customers might rely on it. If it unfortunately breaks something, they can easily mitigate that by setting the RESCALING flag explicitly and report the bug.  
  
So my proposal is to wait with this for at least one release, or maybe even 3. (We can see in after next release what we get). Then we can remove all the robustness policies and the rescaling policy itself.

---

## Review 4 [Approved]

> Username: vissarion  
> Created_at: 2022-02-18 14:25:25 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/977#pullrequestreview-887342456  

Great, thanks!

---

## Comment 5

> Username: awulkiew  
> Created_at: 2022-02-18 14:33:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/977#issuecomment-1044619265  

@barendgehrels Ok, I agree. Should we deprecate it now i.e. generate a `#warning` if rescaling is enabled?

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2022-02-19 11:26:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/977#issuecomment-1045995482  

> @barendgehrels Ok, I agree. Should we deprecate it now i.e. generate a `#warning` if rescaling is enabled?  
  
Good idea, I’ll do that. And I’ve to update some of the docs too.  
  
Great that we will switch now! Really cool.

---

## Review 7 [Commented]

> Username: barendgehrels  
> Created_at: 2022-02-23 10:39:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/977#pullrequestreview-890892705  

📁 include/boost/geometry/core/config.hpp

```diff
  32 |+ 
  33 |+ #if defined(BOOST_GEOMETRY_USE_RESCALING) && defined(BOOST_GEOMETRY_NO_ROBUSTNESS)
  34 |+     #error "Define either BOOST_GEOMETRY_NO_ROBUSTNESS (default) or BOOST_GEOMETRY_USE_RESCALING"
```

> Username: barendgehrels  
> Created_at: 2022-02-23 10:39:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/977#discussion_r812758506  

We didn't have it before. It's similar to, for example:  
https://github.com/boostorg/chrono/blob/develop/include/boost/chrono/config.hpp#L22


---

## Comment 8

> Username: barendgehrels  
> Created_at: 2022-02-23 10:43:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/977#issuecomment-1048651791  

> @barendgehrels Ok, I agree. Should we deprecate it now i.e. generate a `#warning` if rescaling is enabled?  
  
:heavy_check_mark:   
  
Done. But not if the default is flipped. I changed the config a bit, if you would check that, and it's fine, we can merge.  
It's a major change so it should be in **master** by March 2

---

## Review 9 [Commented]

> Username: barendgehrels  
> Created_at: 2022-02-23 10:44:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/977#pullrequestreview-890898460  

📁 include/boost/geometry/strategy/spherical/envelope_range.hpp

```diff
 168 |-     using coord_t = typename geometry::coordinate_type<point_t>::type;
 169 |-     using units_t = typename geometry::detail::cs_angular_units<point_t>::type;
 170 |-     using constants_t = math::detail::constants_on_spheroid<coord_t, units_t>;
```

> Username: barendgehrels  
> Created_at: 2022-02-23 10:44:09 UTC  
> Updated_at: 2022-02-23 10:44:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/977#discussion_r812762751  

This is unrelated, but it gave me warnings in many files I compiled.  
So I thought it's better to get this in too on short term.


---

## Review 10 [Commented]

> Username: barendgehrels  
> Created_at: 2022-02-23 10:46:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/977#pullrequestreview-890900673  

📁 include/boost/geometry/core/config.hpp

```diff
  28 | 
  29 |+ #if defined(BOOST_GEOMETRY_USE_RESCALING) && ! defined(BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE)
  30 |+     BOOST_PRAGMA_MESSAGE("Rescaling is deprecated and its functionality will be removed in Boost 1.82")
```

> Username: barendgehrels  
> Created_at: 2022-02-23 10:46:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/977#discussion_r812764321  

Is this right? `1.79` is next, `1.80` is second, `1.81` is third so we can remove it in `1.82`?  
Which is around a year.

> Username: awulkiew  
> Created_at: 2022-02-23 10:55:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/977#discussion_r812771783  

I think it's ok.


---

## Comment 11

> Username: barendgehrels  
> Created_at: 2022-02-23 11:50:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/977#issuecomment-1048702566  

Merged, thanks for your quick responses!

---
