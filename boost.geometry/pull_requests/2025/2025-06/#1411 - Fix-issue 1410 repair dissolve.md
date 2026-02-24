# #1411 Fix/issue 1410 repair dissolve [Open]

> Username: barendgehrels  
> Created at: 2025-06-25 17:02:44 UTC  
> Updated at: 2025-12-08 15:33:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1411  

This PR:  
  
* prepares the traverse algorithm such that it supports `dissolve` better  
* fixes compilation in `dissolve`  
* fixes the unit test (and adding geojson)  
* **add an alternative using buffer**  
* **add an alternative using correct** as submitted in #868   
  
Test results:  
**original case**  
  
It is mostly as it was before. Note that the versions 1.85 did not run without errors either. Removing duplicate cases,  
there are just two errors now.  
```  
Running 1 test case...  
dissolve.cpp:147: dissolve: ggl_list_denis #clips expected: 2 detected: 1 type: d  
dissolve.cpp:150: difference{23.2553} between length_or_area{870.87609377304386} and expected_area{21123.328099999999} exceeds 0.001%  
  
dissolve.cpp:150: difference{0.171709} between length_or_area{30.711676315570951} and expected_area{26.210999999999999} exceeds 0.001%  
```  
  
**using buffer**  
The expectations are a for some cases adapted, because the version using buffer always removes any interior ring.  
`*** No errors detected`  
  
**using correct**  
The expectations are as in the original cases.  
`*** No errors detected`

---

## Review 1 [Commented]

> Username: tinko92  
> Created_at: 2025-06-30 02:53:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1411#pullrequestreview-2969753501  

The changes look good to me (make all calls consistent with the removal of rescale policies, replace svg mapper with geojson in testing). I noticed no new failures in the main test suite, as expected based on the changes.  
  
Two things I noticed, though:  
1) line 13, run dissolve.cpp is still commented out in geometry/extensions/test/algorithms/Jamfile in your branch. If I run that test, I get 63 failures on my only tested configuration linux, aarch64 with gcc (7, 8, 9, 12, 13, star_{a, b, c}, mail_2017_09_24_{e,f}, mail_2017_09_30_a, reallife, gitter_2013_04_b, ggl_list_denis, mail_2018_08_19 all with types f and d). That does confirm that it compiles, though.  
2) The compilation issues of dissolve would have been caught by a minimal test. Would we want such a test as part of the CI for extensions which are intended for future inclusion and active usage?

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2025-07-05 09:30:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1411#issuecomment-3038609257  

Thanks for your remarks!  
  
> Two things I noticed, though:  
  
> 1. line 13, run dissolve.cpp is still commented out in geometry/extensions/test/algorithms/Jamfile in your branch. If I run that test, I get 63 failures on my only tested configuration linux,  
  
Indeed. The results are different than from before, I still have to work on that.  
  
That does confirm that it compiles, though.  
  
Indeed  
  
> 2. The compilation issues of dissolve would have been caught by a minimal test. Would we want such a test as part of the CI for extensions which are intended for future inclusion and active usage?  
  
Good question. We don't have them for extensions. They are meant to be  
* candidates for the "real" library (though some already for way too long)  
* extra functionality which will not make it  
* maybe I miss some.  
  
Also they should not be part of `master` and therefore I always split commits (extensions only / no extension code affected). Integrating it will make it (maybe) more inconvenient.  
  
@vissarion what are your thoughts?

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2025-07-19 12:13:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1411#issuecomment-3092319342  

I first wanted to make a follow-up. But because it is not merged (neither approved) anyway, it can be done in this PR

---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-19 12:39:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1411#pullrequestreview-3035364094  

📁 include/boost/geometry/extensions/algorithms/dissolve_using_correct.hpp

```diff
 116 |+ };
 117 |+ 
 118 |+ template <typename Ring, typename PseudoVertices>
```

> Username: barendgehrels  
> Created_at: 2025-07-19 12:39:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1411#discussion_r2217308129  

The structure (incl. alignment) of the original file is kept.  
What is changed is:  
```  
template<  
	typename point_t = boost::geometry::model::d2::point_xy<double>,   
	typename ring_t = boost::geometry::model::ring<point_t>  
	>  
```  
-> (in this case, and also everywhere below)  
```  
template <typename Ring, typename PseudoVertices>  
```  
  
sometimes using `point_type_t`, sometimes another small change. Such that it can be used for any type.  
  
And some minor related changes.


---

## Review 5 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-19 12:40:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1411#pullrequestreview-3035364334  

📁 include/boost/geometry/extensions/algorithms/dissolve_using_correct.hpp

```diff
 285 |+             }
 286 |+ 
 287 |+ 			// Repeat until back at starting point
```

> Username: barendgehrels  
> Created_at: 2025-07-19 12:40:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1411#discussion_r2217308430  

I believe this `while` can lead to infinite loops. I had it once when changing a thingy.  
But in the current unit test, it is always fine.


---

## Review 6 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-19 12:41:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1411#pullrequestreview-3035364426  

📁 include/boost/geometry/extensions/algorithms/dissolve_using_correct.hpp

```diff
 480 |+ 
 481 |+ template <typename Polygon, typename MultiPolygon, typename Area>
 482 |+ inline void correct_non_zero(Polygon const &input, MultiPolygon &output, Area remove_spike_min_area = 0.0)
```

> Username: barendgehrels  
> Created_at: 2025-07-19 12:41:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1411#discussion_r2217308540  

it is renamed because boost::geometry has already a function called `correct`


---

## Review 7 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-19 12:41:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1411#pullrequestreview-3035364511  

📁 include/boost/geometry/extensions/algorithms/dissolve_using_correct.hpp

```diff
 490 |+ 
 491 |+ template<typename Polygon, typename MultiPolygon, typename Area>
 492 |+ inline void correct_odd_even(Polygon const &input, MultiPolygon &output, Area remove_spike_min_area = 0.0)
```

> Username: barendgehrels  
> Created_at: 2025-07-19 12:41:48 UTC  
> Updated_at: 2025-07-19 12:41:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/1411#discussion_r2217308647  

This function is not unit tested, in this PR (it can and gives other results obviously)


---

## Review 8 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-19 12:42:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1411#pullrequestreview-3035364614  

📁 include/boost/geometry/extensions/algorithms/dissolve_using_correct.hpp

```diff
 506 |+ 
 507 |+ template <typename MultiPolygon, typename Area>
 508 |+ inline void correct_non_zero(MultiPolygon const &input, MultiPolygon &output, Area remove_spike_min_area = 0.0)
```

> Username: barendgehrels  
> Created_at: 2025-07-19 12:42:19 UTC  
> Updated_at: 2025-07-19 12:42:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1411#discussion_r2217308760  

This is an overload, and I kept it.  
If we productize it we should use the normal `dispatch` mechanism.


---

## Comment 9

> Username: barendgehrels  
> Created_at: 2025-07-19 12:50:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/1411#issuecomment-3092335263  

I will add some pictures later this weekend.

---

## Review 10 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-31 17:04:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1411#pullrequestreview-3076249088  

📁 include/boost/geometry/algorithms/detail/overlay/graph/traverse_graph.hpp

```diff
 191 |+         while (iteration_count < m_turns.size())
 192 |+         {
 193 |+             auto const current_turn_indices = get_turn_indices_by_node_id(m_turns, m_clusters,
```

> Username: barendgehrels  
> Created_at: 2025-07-31 17:04:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1411#discussion_r2245935468  

Code within this while-loop is all the same as before.  
But we don't call itself recursively anymore (for large geometries on some operating systems, this could result in a stack overflow). It is just a while loop now which was a trivial change, and much better. If there are many turns (for example for equal geometries), it can loop for (for example) more than 3000 times which should not cause any problem.


---

## Review 11 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-31 17:09:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1411#pullrequestreview-3076266466  

📁 include/boost/geometry/algorithms/detail/overlay/graph/traverse_graph.hpp

```diff
 255 |+             }
 256 | 
 247 |-         return continue_traverse(ring, component_id, start_node_id, next_target_node_id);
```

> Username: barendgehrels  
> Created_at: 2025-07-31 17:09:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1411#discussion_r2245946175  

This was a recursive call, not wise in hindsight.   
@vissarion We should get this fix in `1.89`. It is still allowed. I will make a separate PR for this.  
It causes problems in big polygons with lots of turns (for example two equal polygons of 3500 points).


---

## Comment 12

> Username: vissarion  
> Created_at: 2025-12-08 15:33:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1411#issuecomment-3627520558  

Sorry for being late reviewing here. I saw that some changes are separated in another PR and merged. @barendgehrels what is the status of this PR?

---
