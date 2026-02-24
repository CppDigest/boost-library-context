# #532 Documentation fixes. [Merged]

> Username: awulkiew  
> Created at: 2018-11-29 01:25:05 UTC  
> Updated at: 2018-11-29 16:22:04 UTC  
> Merged at: 2018-11-29 16:21:57 UTC  
> Closed at: 2018-11-29 16:21:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/532  

1. Wrong arguments' names of `discrete_hausdorff_distance()`  
2. Wrong description of `distance()`  
https://github.com/boostorg/geometry/issues/521  
3. Missing documentation of `dsv()`  
https://github.com/boostorg/geometry/issues/520

---

## Review 1 [Commented]

> Username: mloskot  
> Created_at: 2018-11-29 09:02:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/532#pullrequestreview-179685352  

📁 include/boost/geometry/algorithms/detail/distance/interface.hpp

```diff
 326 | \details
 326 |- \details \details_calc{area}. \brief_strategy. \details_strategy_reasons
 327 |+ \details The free function distance calculates the distance between two geometries \brief_strategy. \details_strategy_reasons
```

> Username: mloskot  
> Created_at: 2018-11-29 09:02:35 UTC  
> Updated_at: 2018-11-29 13:11:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/532#discussion_r237399538  

Shouldn't it read  
  
> The free `distance` function calculates...?

> Username: awulkiew  
> Created_at: 2018-11-29 12:48:17 UTC  
> Updated_at: 2018-11-29 13:11:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/532#discussion_r237472804  

It's from a template we use:  
https://github.com/boostorg/geometry/blob/develop/doc/doxy/Doxyfile#L98  
  
So either we change the template everywhere or leave it here as it is. Personally I like the synthetic version.

> Username: mloskot  
> Created_at: 2018-11-29 14:24:40 UTC  
> Updated_at: 2018-11-29 14:24:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/532#discussion_r237505841  

I forgot about it. The template makes sense. It's just the coincidence of `distance` and `distance` that may read weird :)

> Username: awulkiew  
> Created_at: 2018-11-29 15:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/532#discussion_r237535293  

Ah, indeed there is repetition. How about something like `The free function distance calculates the length of the shortest path between two geometries`?

> Username: mloskot  
> Created_at: 2018-11-29 16:10:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/532#discussion_r237551897  

I think it's good, for hand-written sentences. But, for the template, I think the current template is acceptable.


---

## Review 2 [Commented]

> Username: mloskot  
> Created_at: 2018-11-29 09:03:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/532#pullrequestreview-179685519  

📁 include/boost/geometry/algorithms/detail/distance/interface.hpp

```diff
 381 | \ingroup distance
 381 |- \details The default strategy is used, corresponding to the coordinate system of the geometries
 382 |+ \details The free function distance calculates the distance between two geometries. \details_default_strategy
```

> Username: mloskot  
> Created_at: 2018-11-29 09:03:02 UTC  
> Updated_at: 2018-11-29 13:11:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/532#discussion_r237399667  

Shouldn't it read  
  
> The free `distance` function calculates...?


---

## Review 3 [Commented]

> Username: mloskot  
> Created_at: 2018-11-29 09:03:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/532#pullrequestreview-179685679  

📁 include/boost/geometry/algorithms/discrete_hausdorff_distance.hpp

```diff
 266 | /*!
 267 |- \brief calculate discrete hausdorff distance between two geometries ( currently works for LineString-LineString, MultiPoint-MultiPoint, Point-MultiPoint, MultiLineString-MultiLineString ) using specified strategy
 267 |+ \brief Calculate discrete hausdorff distance between two geometries (currently
```

> Username: mloskot  
> Created_at: 2018-11-29 09:03:30 UTC  
> Updated_at: 2018-11-29 13:11:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/532#discussion_r237399818  

I think it should be capitalised  `Hausdorff`

> Username: awulkiew  
> Created_at: 2018-11-29 12:59:06 UTC  
> Updated_at: 2018-11-29 13:11:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/532#discussion_r237476118  

Right, thanks.

> Username: awulkiew  
> Created_at: 2018-11-29 13:13:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/532#discussion_r237480568  

I also fixed Frechet distance the same way.


---

## Review 4 [Commented]

> Username: mloskot  
> Created_at: 2018-11-29 09:03:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/532#pullrequestreview-179685817  

📁 include/boost/geometry/algorithms/discrete_hausdorff_distance.hpp

```diff
 310 | /*!
 304 |- \brief calculate discrete hausdorff distance between two geometries ( currently works for LineString-LineString, MultiPoint-MultiPoint, Point-MultiPoint, MultiLineString-MultiLineString )
 311 |+ \brief Calculate discrete hausdorff distance between two geometries (currently
```

> Username: mloskot  
> Created_at: 2018-11-29 09:03:49 UTC  
> Updated_at: 2018-11-29 13:11:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/532#discussion_r237399919  

I think it should be capitalised  `Hausdorff`


---

## Comment 5

> Username: mloskot  
> Created_at: 2018-11-29 09:04:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/532#issuecomment-442757573  

LGTM, just posted a few minor comments. Thanks!

---
