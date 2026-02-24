# #1367 feat: include changes and tests changes [Merged]

> Username: barendgehrels  
> Created at: 2025-01-15 21:24:41 UTC  
> Updated at: 2025-03-18 13:20:21 UTC  
> Merged at: 2025-01-17 18:05:08 UTC  
> Closed at: 2025-01-17 18:05:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367  

Various changes done for traversal change, but put into a separate PR

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-15 21:25:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1367#pullrequestreview-2554007000  

📁 include/boost/geometry/algorithms/detail/overlay/traversal.hpp

```diff
  24 | 
  25 | #include <boost/geometry/algorithms/detail/overlay/cluster_info.hpp>
  26 |+ #include <boost/geometry/algorithms/detail/overlay/debug_traverse.hpp>
```

> Username: barendgehrels  
> Created_at: 2025-01-15 21:25:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1917353405  

This file will be removed. But the debug info is used elsewhere, so it's moved out


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-15 21:25:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1367#pullrequestreview-2554008508  

📁 include/boost/geometry/strategies/relate/cartesian.hpp

```diff
  24 | 
  25 |+ #include <boost/geometry/strategies/distance/detail.hpp>
  26 |+ #include <boost/geometry/strategies/distance/services.hpp>
```

> Username: barendgehrels  
> Created_at: 2025-01-15 21:25:42 UTC  
> Updated_at: 2025-01-15 21:25:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1917353927  

Traveral will use the `distance` strategy from the umbrella strategy

> Username: awulkiew  
> Created_at: 2025-01-16 14:20:56 UTC  
> Updated_at: 2025-01-16 14:23:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1918646512  

I guess this is ok for now.  
  
So it seems there are dependency problems with umbrella strategies right now:  
- relate needs parts of distance  
- distance needs relate and AFAIR index (right now besides distance index it also includes centroid)  
- index needs distance and relate  
  
The simplest solution would be to put everything in one umbrella strategy.  
It would also be possible to create strategies with only what's needed.  
What would you prefer?

> Username: barendgehrels  
> Created_at: 2025-01-16 16:44:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1918878420  

Putting everything in one umbrella sounds reasonable to me.   
  
I still find it hard to not be able to use distance because of the mutual dependency. I've had it several times.  
Resorting then to unwished things like https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp#L188  
But now I cannot escape from it.  
  
We could also look at why distance needs relate. But that's maybe a passed station.

> Username: barendgehrels  
> Created_at: 2025-01-16 17:59:54 UTC  
> Updated_at: 2025-01-16 17:59:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1918976581  

It's now `comparable_distance` - but I don't know if that will make a difference for the discussion.

> Username: awulkiew  
> Created_at: 2025-01-16 21:02:58 UTC  
> Updated_at: 2025-01-16 21:04:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1919192311  

`distance` needs `relate` because it checks for intersection of geometries and returns `0` if that's the case. Consider e.g. `distance(point, polygon)`, `intersects`/`covered_by` has to be checked before or in addition to calculating the distance between point and boundary of polygon.  
  
For sure only a subset of strategy getters is needed. Not all relational operations has to be available in distance. The question is whether or not there are any benefits of dividing the umbrella strategies into parts and maintaining it over putting everything in one strategy. I'm ok with both approaches.  
  
But if we agreed that we should put everything needed by `distance`, `relate` (and therefore also `setops`) and `index` in one umbrella strategy (this also means `area`, `centroid`, `envelope` and `expand` strategies, maybe also `less`/`compare`, I can't remember) then there is not much left. So maybe we should consider having one umbrella strategy per CS defining every strategy needed by any algorithm and deprecate everything else?

> Username: barendgehrels  
> Created_at: 2025-01-17 06:46:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1919638119  

Thanks for the backgrounds, let's come back to that elsewhere.  
  
Yes, if most umbrella strategies contain nearly everything - let's make one?  
But is that really true? Some simpler algorithms, simplify, for example, needs just two strategies (AFAIR). Convex hull will not use much either. Maybe we can move this to the discussion section.

> Username: vissarion  
> Created_at: 2025-01-17 15:49:00 UTC  
> Updated_at: 2025-01-17 16:03:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1920387052  

We have to investigate if there are many "simple" algorithms that needs just few strategies. I find it simpler just to create one umbrella strategy and use it everywhere. Anyway, I agree to move this to discussions.


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-15 21:26:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1367#pullrequestreview-2554011073  

📁 test/algorithms/buffer/CMakeLists.txt

```diff
  43 |+     buffer_countries
  44 |+     buffer_linestring
  45 |+     buffer_linestring_geo
```

> Username: barendgehrels  
> Created_at: 2025-01-15 21:26:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1917354870  

The `buffer_linestring_geo` was not yet in cmake lists.  
It's added now but very slow. Likewise `buffer_linestring` is slow.  
They are not run by default now.


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-15 21:28:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1367#pullrequestreview-2554015980  

📁 test/robustness/CMakeLists.txt

```diff
   7 | 
   8 |+ 
   9 |+ function(boost_geometry_add_robustness_test item)
```

> Username: barendgehrels  
> Created_at: 2025-01-15 21:28:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1917356693  

The robustness tests were compiled and run with ctest.  
For the robustness tests that makes no sense and costs (depending on the options) a lot of time.  
Therefore it's now a separate target, and not added to the test framework.


---

## Review 5 [Commented]

> Username: tinko92  
> Created_at: 2025-01-16 15:56:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1367#pullrequestreview-2556460610  

📁 test/algorithms/buffer/buffer_countries.cpp

```diff
 123 |+     {
 124 |+         // If this file is not found, the others won't be find either.
 125 |+         return;
```

> Username: tinko92  
> Created_at: 2025-01-16 15:12:47 UTC  
> Updated_at: 2025-01-16 15:56:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1918735332  

Maybe it is dangerous to return silently without warning that the test could be conducted or without returning a failure. (I do not know the history/purpose of that test though)

> Username: barendgehrels  
> Created_at: 2025-01-16 16:45:28 UTC  
> Updated_at: 2025-01-16 16:45:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1918879727  

It should not be used as a standard unit test.  
But yes, indeed - I can add a message. Will do.  
Good point.

> Username: barendgehrels  
> Created_at: 2025-01-16 18:05:10 UTC  
> Updated_at: 2025-01-16 18:05:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1918982533  

✅ done


📁 test/algorithms/set_operations/union/union_other_types.cpp

```diff
 146 |+     // Boost multi precision ratio is currently NOT supported
 147 |+     // The sqrt is not supported for it, which would limit it support
 148 |+     // drastically to a very limited set of operations.
```

> Username: tinko92  
> Created_at: 2025-01-16 15:49:37 UTC  
> Updated_at: 2025-01-16 15:56:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1918794395  

This is not obvious for set operations to me. Will the upcoming major PR use distance in set operations in a context where comparable distance could not be used?

> Username: barendgehrels  
> Created_at: 2025-01-16 16:46:57 UTC  
> Updated_at: 2025-01-16 16:50:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1918881818  

Indeed. I cannot use comparable distance because I'm summing distances.  
Essentially, for a cc turn (both continue), we need to know if we in the end go left or right, and where.  
As long as the segments continue, I need to sum their lengths.

> Username: barendgehrels  
> Created_at: 2025-01-16 16:52:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1918889900  

While writing this, and re-reading, I realize: if they all continue, it's a straight line and could be computed with one call for comparable distance for the turn point and the end point.  
I'll try to change it. Thanks for the comment.

> Username: barendgehrels  
> Created_at: 2025-01-16 18:06:26 UTC  
> Updated_at: 2025-01-16 18:06:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1918983828  

✅ It was possible and gave identical results.  
So it is changed, and these changes in `union_other_types.cpp` could be reverted now.  
Not that I think it will be very useful to use `boost::rational` - but at least it is not a change anymore.  
Thanks again.


📁 test/algorithms/buffer/read_from_wkt_file.hpp

```diff
  39 |     }
  40 | 
  41 |-     BOOST_CHECK(! out.str().empty());
```

> Username: tinko92  
> Created_at: 2025-01-16 15:54:39 UTC  
> Updated_at: 2025-01-16 15:56:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1918802349  

Same as https://github.com/boostorg/geometry/pull/1367#discussion_r1918735332 , this change to silently ignore a missing input seems dangerous in a test context.

> Username: barendgehrels  
> Created_at: 2025-01-16 16:47:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1918882702  

So yes, I'll keep this out (it let the test fails) but will add a message.


---

## Review 6 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-17 06:48:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1367#pullrequestreview-2558092245  

📁 include/boost/geometry/strategies/relate/geographic.hpp

```diff
  83 |+     template <typename Geometry1, typename Geometry2>
  84 |+     auto comparable_distance(Geometry1 const&, Geometry2 const&,
  85 |+                              distance::detail::enable_if_pp_t<Geometry1, Geometry2> * = nullptr) const
```

> Username: barendgehrels  
> Created_at: 2025-01-17 06:48:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1919639763  

I didn't see it used earlier - but I think we should name it like this.


---

## Review 7 [Approved]

> Username: vissarion  
> Created_at: 2025-01-17 16:03:45 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1367#pullrequestreview-2559116599  

LGTM thanks!

📁 include/boost/geometry/algorithms/detail/overlay/debug_traverse.hpp

```diff
   1 |+ // Boost.Geometry (aka GGL, Generic Geometry Library)
   2 |+ 
   3 |+ // Copyright (c) 2007-2024 Barend Gehrels, Amsterdam, the Netherlands.
```

> Username: vissarion  
> Created_at: 2025-01-17 14:09:53 UTC  
> Updated_at: 2025-01-17 16:03:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1920249146  

update copyright to 2025?

> Username: barendgehrels  
> Created_at: 2025-01-17 18:00:32 UTC  
> Updated_at: 2025-01-17 18:00:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1920559105  

✅

---

📁 include/boost/geometry/algorithms/detail/overlay/debug_traverse.hpp

```diff
  13 |+ // http://www.boost.org/LICENSE_1_0.txt)
  14 |+ 
  15 |+ #ifndef BOOST_GEOMETRY_ALGORITHMS_DETAIL_DEBUG_TRAVERSE_HPP
```

> Username: vissarion  
> Created_at: 2025-01-17 14:10:43 UTC  
> Updated_at: 2025-01-17 16:03:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1920250295  

Should be BOOST_GEOMETRY_ALGORITHMS_DETAIL_OVERLAY_DEBUG_TRAVERSE_HPP, right?

> Username: barendgehrels  
> Created_at: 2025-01-17 18:00:39 UTC  
> Updated_at: 2025-01-17 18:00:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/1367#discussion_r1920559252  

✅


---
