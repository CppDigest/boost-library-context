# #857 Fix/buffer cluster order [Merged]

> Username: barendgehrels  
> Created at: 2021-05-26 11:31:04 UTC  
> Updated at: 2021-06-02 09:46:36 UTC  
> Merged at: 2021-06-02 09:46:31 UTC  
> Closed at: 2021-06-02 09:46:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/857  

This fixes the last 88 errors from the `recursive_polygons_buffer` test. Result is now:  
`geometries: 310000 errors: 0 type: d time: 33.439`  
  
There were  `2286` errors when I started to work on this part (many of them had of course the same cause) so I'm glad it's now better than the rescaled version (8 errors).  
  
With this, the version without rescaling is behaving as good, or better, as the rescaling version. See this grep below, where the first value are the detected errors using rescaling. The last 3 values are without rescaling (double, float, long double). There is only one test better now using rescaling, then with double.  
  
So I propose that we change the default to not-rescale this release, and we can deprecate and remove rescaling in one of the coming releases. But I've still to check the tests other than set_operations (area) / buffer. However they should be less influenced.  
  
What are your opinions?  
  
```  
set_operations/union/union_multi.cpp:    BoostGeometryWriteExpectedFailures(9, 0, 1, 0);  
set_operations/union/union.cpp:    BoostGeometryWriteExpectedFailures(3, 1, 2, 0);  
set_operations/difference/difference.cpp:    BoostGeometryWriteExpectedFailures(12, 5, 11, 6);  
set_operations/difference/difference_multi.cpp:    BoostGeometryWriteExpectedFailures(22, 12, 16, 7);  
set_operations/intersection/intersection.cpp:    BoostGeometryWriteExpectedFailures(5, 2, 6, 1);  
set_operations/intersection/intersection_multi.cpp:    BoostGeometryWriteExpectedFailures(9, 1, 2, 1);  
buffer/buffer_point.cpp:    BoostGeometryWriteExpectedFailures(0);  
buffer/buffer_countries.cpp:    BoostGeometryWriteExpectedFailures(1, 0, 2, 0);  
buffer/buffer_multi_polygon.cpp:    BoostGeometryWriteExpectedFailures(5, 1, 3, 4);  
buffer/buffer_linestring.cpp:    BoostGeometryWriteExpectedFailures(2, 4, 9, 4); **  
buffer/buffer_linestring_aimes.cpp:    BoostGeometryWriteExpectedFailures(0);  
buffer/buffer_multi_point.cpp:    BoostGeometryWriteExpectedFailures(0);  
buffer/buffer_polygon.cpp:    BoostGeometryWriteExpectedFailures(2, 1, 9, 1);  
buffer/buffer_multi_linestring.cpp:    BoostGeometryWriteExpectedFailures(9, 6, 9, 3);  
```

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2021-05-26 11:32:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/857#pullrequestreview-668904087  

📁 include/boost/geometry/algorithms/detail/overlay/traversal.hpp

```diff
 607 |+ 
 608 |+         bool const next_in_same_cluster
 609 |+                 = cluster_indices.count(op.enriched.get_next_turn_index()) > 0;
```

> Username: barendgehrels  
> Created_at: 2021-05-26 11:32:15 UTC  
> Updated_at: 2021-05-26 11:32:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r639638747  

This is the fix. In a cluster, it should prefer a way out of the cluster.

> Username: awulkiew  
> Created_at: 2021-05-26 15:57:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r639870684  

`cluster_indices` is of type `std::set<signed_size_type>` which means that `count` returns either `0` or `1`. Is that what was desired or did you mean to use `std::multi_set` or `std::map<signed_size_type, size_t>` to count the indexes or maybe even `std::unordered_map<signed_size_type, size_t>` ?

> Username: barendgehrels  
> Created_at: 2021-05-31 15:10:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r642553718  

Indeed, `count` returns `1` if it is in the set, and that is what I'm using (I like it more than checking `find` and `end`)  
So it should not be a map or a multiset

> Username: awulkiew  
> Created_at: 2021-05-31 15:34:49 UTC  
> Updated_at: 2021-05-31 15:34:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r642566906  

Ok, you probably want to check whether or not an id was found, and how many times is not important. Correct?  
  
Then I'd only suggest to think about `unordered_set<>` because the complexity of the lookup is O(1) on average, instead of O(logN). And AFAIU you only care to find if the id exists and sorting is not important. On the other hand it has higher constant cost than `set<>` and will become faster above some number of ids. So it depends on how many clusters do you expect to deal with.

> Username: barendgehrels  
> Created_at: 2021-05-31 16:55:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r642601553  

Sure, correct.  
  
The number of clusters is usually very low. It’s where turns are colocated and that does not happen often in nature. (In our testsets it happens more because they are constructed for exactly that reason, these cases are harder.)  
  
So just `set` is probably fine then. Thanks for your view.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2021-05-26 11:34:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/857#pullrequestreview-668905631  

📁 test/algorithms/set_operations/difference/difference.cpp

```diff
 631 |     // The difference algorithm can generate (additional) slivers
 639 |-     BoostGeometryWriteExpectedFailures(10, 7, 22, 8);
 632 |+     BoostGeometryWriteExpectedFailures(12, 5, 11, 6);
```

> Username: barendgehrels  
> Created_at: 2021-05-26 11:34:08 UTC  
> Updated_at: 2021-05-26 11:34:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r639639945  

(the 2 extra for rescaled is not a regression, one test now also tests symmetric difference which fails for rescaling but succeeds otherwise)

> Username: vissarion  
> Created_at: 2021-05-27 07:38:10 UTC  
> Updated_at: 2021-05-27 07:44:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r640364657  

sorry, I didn't get where the extra '2' comes from

> Username: barendgehrels  
> Created_at: 2021-05-31 15:10:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r642554112  

From symmetric difference which is now also tested for that case (resulting in 2 errors)


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2021-05-26 15:52:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/857#pullrequestreview-669210078  

📁 include/boost/geometry/algorithms/detail/overlay/traversal.hpp

```diff
 579 |+         // Returns 2: OK
 580 |+         // Returns 3: OK and start turn matches
 581 |+         // Returns 4: OK and start turn and start op both match
```

> Username: awulkiew  
> Created_at: 2021-05-26 15:52:27 UTC  
> Updated_at: 2021-05-26 15:52:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r639866722  

Why not make an enum describing this? These values are not used to calculate something are they?

> Username: barendgehrels  
> Created_at: 2021-05-31 15:14:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r642556045  

I'm not sure about that. We just need an order here, and we don't have to name the states.  
The comments are to explain the ordering and only relevant for this place, and no other place only.  
If it would be an enum (possible of course) we would only assign it and never use it, and it would be more error-prone if people accidentally change the order later (or we should assign values to them)

> Username: awulkiew  
> Created_at: 2021-05-31 15:48:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r642573940  

Ah ok, I see now that this function is called below in `select_from_cluster_union` and what is returned here is some kind of priority. So indeed an enum is not needed because the caller is not concerned about specific values, only about relative priority.  
  
But AFAIU this means that even though this function is called `select_turn_in_cluster_union` it doesn't select anything. It calculates this priority value instead. If my understanding is correct I propose to rename it accordingly. To e.g. `priority_of_turn_in_cluster_union()` or whatever you think reflects values that are returned the best.

> Username: barendgehrels  
> Created_at: 2021-05-31 16:56:27 UTC  
> Updated_at: 2021-05-31 16:56:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r642601824  

Thanks, good idea, `priority...` is good, I will rename the function

> Username: barendgehrels  
> Created_at: 2021-06-02 08:18:04 UTC  
> Updated_at: 2021-06-02 08:18:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r643757822  

:heavy_check_mark:


---

## Comment 4

> Username: awulkiew  
> Created_at: 2021-05-26 16:06:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#issuecomment-848902220  

>What are your opinions?  
  
Thanks! I'm all for removing rescaling.  
  
Are all of the MySQL test cases in various algorithms passing?

---

## Review 5 [Approved]

> Username: vissarion  
> Created_at: 2021-05-27 07:44:09 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/857#pullrequestreview-669833072  

Thanks, I have a few questions but in general I am OK for merging.

📁 include/boost/geometry/algorithms/detail/overlay/traversal.hpp

```diff
 609 |+                 = cluster_indices.count(op.enriched.get_next_turn_index()) > 0;
 610 |+ 
 611 |+         return to_start && to_start_index ? 4
```

> Username: vissarion  
> Created_at: 2021-05-27 07:30:14 UTC  
> Updated_at: 2021-05-27 07:44:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r640359242  

+1 for using `enum` here

> Username: barendgehrels  
> Created_at: 2021-06-02 09:36:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r643790037  

It's now a `priority`, see conversation with Adam. I assume that's OK for you too.

---

📁 include/boost/geometry/algorithms/detail/overlay/traversal.hpp

```diff
 678 |                 = select_turn_in_cluster_union(selected_rank, ranked_point,
 679 |+                                                cluster_indices,
 680 |                     start_turn_index, start_op_index);
```

> Username: vissarion  
> Created_at: 2021-05-27 07:30:51 UTC  
> Updated_at: 2021-05-27 07:44:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r640359667  

indentation issue here

> Username: barendgehrels  
> Created_at: 2021-05-31 15:18:47 UTC  
> Updated_at: 2021-06-02 08:18:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r642558404  

Thanks, conventions changed...  
:heavy_check_mark: fixed


📁 test/algorithms/set_operations/difference/difference.cpp

```diff
 346 | 
 347 |     {
 348 |-         //geos_2_a #area expected: 138.69238279999999008 detected: 138.53125 type: f
```

> Username: vissarion  
> Created_at: 2021-05-27 07:35:41 UTC  
> Updated_at: 2021-05-27 07:44:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r640362913  

is this replaced by `expectation_limits` below? Should we keep stating the library and version  (geos?) used to produce the expected results?

> Username: barendgehrels  
> Created_at: 2021-05-31 15:20:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r642559199  

In the past I incidentally mentioned used libraries or tools, but it's too much work to do that everywhere.  
And now that we have a range of allowed values it's kind of redundant.

---

📁 test/algorithms/set_operations/difference/difference.cpp

```diff
 351 |-         // MSVC 14 expects 138.69214 and 211.85913: increase percentage
 351 |         // Output polygons for sym difference might be combined
 352 |+         expectation_limits a{138.5312, 138.6924};
```

> Username: vissarion  
> Created_at: 2021-05-27 07:36:21 UTC  
> Updated_at: 2021-05-27 07:44:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r640363400  

where are these numbers come from?

> Username: barendgehrels  
> Created_at: 2021-05-31 15:15:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r642556746  

From latest tests.  
Especially if there are nearly collinear segments, the calculated areas can differ.  
If they differ I always check them visually (with SVG's)


📁 test/robustness/overlay/buffer/recursive_polygons_buffer.cpp

```diff
 154 |         stream << bg::wkt(mp) << std::endl;
 156 |-         stream << bg::wkt(buffer) << std::endl;
 155 |+         if (false)
```

> Username: vissarion  
> Created_at: 2021-05-27 07:42:42 UTC  
> Updated_at: 2021-05-27 07:44:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r640367745  

is this part of code needed?

> Username: barendgehrels  
> Created_at: 2021-05-31 15:21:12 UTC  
> Updated_at: 2021-06-02 08:20:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r642559741  

Not really, I can omit it. If it is wished it's trivial to add again.  
:heavy_check_mark: fixed

---

📁 test/robustness/overlay/buffer/recursive_polygons_buffer.cpp

```diff
 218 |     bg::strategy::buffer::side_straight side_strategy;
 216 |-     bg::strategy::buffer::join_round join_round_strategy(32); // Compatible with MySQL
 219 |+     bg::strategy::buffer::join_round join_round_strategy(settings.points_per_circle);
```

> Username: vissarion  
> Created_at: 2021-05-27 07:43:30 UTC  
> Updated_at: 2021-05-27 07:44:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r640368316  

what does "compatible with MySQL" means and why it is removed ?

> Username: barendgehrels  
> Created_at: 2021-05-31 15:16:46 UTC  
> Updated_at: 2021-06-02 08:21:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#discussion_r642557379  

It's not removed, and 32 still the default.  But it's flexible now.  
It means that MySQL also uses 32 points per circle - but actually for this test that is not very relevant so I removed that comment here.  
:heavy_check_mark: I mentioned MySQL now where it is initialized


---

## Comment 6

> Username: barendgehrels  
> Created_at: 2021-05-31 15:14:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#issuecomment-851553027  

> Are all of the MySQL test cases in various algorithms passing?  
  
I'll check

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2021-06-02 08:47:03 UTC  
> Updated_at: 2021-06-02 09:19:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#issuecomment-852845261  

Code is updated

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2021-06-02 09:34:59 UTC  
> Updated_at: 2021-06-02 09:35:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#issuecomment-852871827  

> I'll check  
  
I see one MySQL cases which is still failing (`mysql_23023665_6` for `difference`), and, on the other hand, one case (`touching1_1` in `buffer_multi_linestring` which is derived from `mysql_23023665_1`) which is now succeeding  
  
So it looks in balance. But I will much do more extensive tests first, before really changing the default behavior, and keep these cases in mind.

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2021-06-02 09:37:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#issuecomment-852873690  

@awulkiew OK to merge?

---

## Comment 10

> Username: awulkiew  
> Created_at: 2021-06-02 09:42:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#issuecomment-852878324  

@barendgehrels Yes, I'm ok with merging.  
  
Before disabling rescaling please make sure that this test is passing as well.

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2021-06-02 09:46:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/857#issuecomment-852881309  

> @barendgehrels Yes, I'm ok with merging.  
>   
> Before disabling rescaling please make sure that this test is passing as well.  
  
Sure, I will investigate it now. Thanks.

---
