# #1129 use auto instead of iterator types, and related [Merged]

> Username: barendgehrels  
> Created at: 2023-04-10 18:50:58 UTC  
> Updated at: 2023-06-16 14:04:07 UTC  
> Merged at: 2023-04-23 11:31:00 UTC  
> Closed at: 2023-04-23 11:31:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129  

It's a big PR but quite mechanical

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2023-04-10 18:52:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1377966688  

📁 include/boost/geometry/algorithms/centroid.hpp

```diff
 357 |-             it != boost::end(multi);
 358 |-             ++it)
 353 |+         for (auto it = boost::begin(multi); it != boost::end(multi); ++it)
```

> Username: barendgehrels  
> Created_at: 2023-04-10 18:52:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1161986157  

NOTE: we cannot do a range based for loop here  
(as @awulkiew pointed out once).  
  
In some places I changed it to a range based for loop, but these places are internal (for example turns).


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-10 20:36:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378100796  

📁 include/boost/geometry/algorithms/densify.hpp

```diff
  99 |-             point_t const& p1 = *it;
  95 |+             auto const& p0 = *prev;
  96 |+             auto const& p1 = *it;
```

> Username: awulkiew  
> Created_at: 2023-04-10 20:36:02 UTC  
> Updated_at: 2023-04-11 09:44:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162066554  

Note that here iterator could return non-true reference type. Previously this type was converted to `point_t` which was value_type vs reference_type returned by iterator. If this non-true reference type was not adapted to Point concept the call to convert below could fail to compile. I don't know if that's a problem.

> Username: barendgehrels  
> Created_at: 2023-04-11 08:17:50 UTC  
> Updated_at: 2023-04-11 08:17:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162460164  

Thanks for your comments! I'll process them soon

> Username: barendgehrels  
> Created_at: 2023-04-12 07:35:03 UTC  
> Updated_at: 2023-04-12 07:35:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163740212  

I'm not sure either. Reverted (it was not related to the iterator anyway)  
:heavy_check_mark:


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-10 20:39:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378104394  

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 777 | 
 794 |-         typename Range::const_iterator it = boost::begin(range);
 778 |+         auto it = std::cbegin(range);
```

> Username: awulkiew  
> Created_at: 2023-04-10 20:39:19 UTC  
> Updated_at: 2023-04-10 20:39:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162069334  

To double-check, is `std` intended here? `boost::size` is called above.

> Username: barendgehrels  
> Created_at: 2023-04-12 07:39:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163744512  

Reverted. First I changed some `begin` but later decided it's better to limit this to iterators only. This was a left over.  
:heavy_check_mark:   
  
Unless there are objections, a separate PR could change `boost::begin`  to `std::begin` everywhere (+`end`)

> Username: barendgehrels  
> Created_at: 2023-04-12 12:01:00 UTC  
> Updated_at: 2023-04-12 12:01:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1164030618  

I did some more checks, we can't change it everywhere. Because we support geometries adapted to Boost.Range, but not `std::begin` etc.  
We might (ever) change that, but that might break behavior for users having custom geometries.


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-10 20:42:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378107631  

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 404 |             // Update member used
 418 |-             turn.turn_index = index;
 405 |+             turn.turn_index = index++;
```

> Username: awulkiew  
> Created_at: 2023-04-10 20:42:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162071510  

`for_each_with_index` is used below. Would it make sense to use it here as well?

> Username: barendgehrels  
> Created_at: 2023-04-11 08:18:18 UTC  
> Updated_at: 2023-04-11 08:18:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162460677  

It's currently a const version only.  
But I can add an overload.

> Username: barendgehrels  
> Created_at: 2023-04-12 07:45:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163751970  

:heavy_check_mark:


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-10 20:44:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378109749  

📁 include/boost/geometry/algorithms/detail/calculate_point_order.hpp

```diff
  38 | 
  39 |-     typename boost::iterators::iterator_reference<Iter>::type ref() const
  39 |+     auto ref() const
```

> Username: awulkiew  
> Created_at: 2023-04-10 20:44:19 UTC  
> Updated_at: 2023-04-11 09:49:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162072956  

This way the reference will be converted to value. The function should probably return `decltype(auto)`.

> Username: barendgehrels  
> Created_at: 2023-04-12 07:47:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163753221  

:heavy_check_mark: reverted

> Username: awulkiew  
> Created_at: 2023-04-14 12:49:38 UTC  
> Updated_at: 2023-04-14 12:49:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1166797271  

FYI, revert was not required. Returning `decltype(auto)` would be enough.

> Username: barendgehrels  
> Created_at: 2023-04-16 12:38:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1167895683  

:heavy_check_mark: intuitively it looked the same, but I get it now. Changed. Thank you.


---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-10 20:46:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378112561  

📁 include/boost/geometry/algorithms/detail/counting.hpp

```diff
  92 |-              it != boost::end(geometry);
  93 |-              ++it)
  90 |+         for (auto const& geometry : multi)
```

> Username: awulkiew  
> Created_at: 2023-04-10 20:46:53 UTC  
> Updated_at: 2023-04-10 20:46:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162074733  

Range-based for loop is probably incorrect here.

> Username: barendgehrels  
> Created_at: 2023-04-12 07:48:38 UTC  
> Updated_at: 2023-04-12 07:48:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163754766  

:heavy_check_mark: right, reverted


---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-11 08:27:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378708676  

📁 include/boost/geometry/algorithms/detail/envelope/range_of_boxes.hpp

```diff
 274 |-              it != boost::end(range_of_boxes);
 275 |-              ++it)
 264 |+         for (auto const& box : range_of_boxes)
```

> Username: awulkiew  
> Created_at: 2023-04-11 08:27:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162471714  

While it's true that currently this function is used only internally I wouldn't be so eager to replace this loop with range-based version. It's because technically we could introduce MultiBox concept in the future. In general I propose to leave normal loops in algorithms taking ranges as template parameters as if they were geometries because replacing them may have unintended consequences for users.

> Username: barendgehrels  
> Created_at: 2023-04-12 07:50:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163756561  

:heavy_check_mark: good point, reverted (keeping `box`)


---

## Review 8 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-11 08:33:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378719005  

📁 include/boost/geometry/algorithms/detail/is_valid/has_spikes.hpp

```diff
  84 |-         iterator next = find_different_from_first(cur, boost::end(view),
  85 |-                                                   strategy);
  82 |+         auto next = find_different_from_first(cur, boost::end(view), strategy);
```

> Username: awulkiew  
> Created_at: 2023-04-11 08:33:51 UTC  
> Updated_at: 2023-04-11 08:39:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162478608  

We're loosing information here. Previously a reader was able to see that `next` was indeed an iterator. However now we don't know what it is. It might be a point or an iterator or something else. I'm thinking about a rule to always have `it` in the name for `auto` iterators. So `cur_it` and `next_it`. Does it make sense?  
EDIT: btw, I'm not sure if that's a good idea. ;)

> Username: barendgehrels  
> Created_at: 2023-04-12 07:54:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163761121  

We often use `next`. The code currently does not use `next_it`.  
I kept it here.   
But I missed the one on line 80/78 earlier, that's done now :heavy_check_mark:

> Username: barendgehrels  
> Created_at: 2023-04-12 10:00:31 UTC  
> Updated_at: 2023-04-12 10:00:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163909526  

We have `it_min` though. So `it_next` would work. But I didn't change it, we can consider what we should do in general.


---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-11 08:36:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378723777  

📁 include/boost/geometry/algorithms/detail/num_distinct_consecutive_points.hpp

```diff
  63 |             ++counter;
  66 |-             iterator next = std::find_if(current, end, [&](auto const& pt) {
  64 |+             auto next = std::find_if(current, end, [&](auto const& pt) {
```

> Username: awulkiew  
> Created_at: 2023-04-11 08:36:35 UTC  
> Updated_at: 2023-04-11 08:36:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162481842  

Though here the names doesn't bother me because I can see what they are from the context.


---

## Review 10 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-11 08:40:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378731075  

📁 include/boost/geometry/algorithms/detail/overlay/assign_parents.hpp

```diff
 287 |-                                        item.envelope, strategy);
 280 |+                     geometry::envelope(get_ring<tag1>::apply(pair.first, geometry1),
 281 |+                                     item.envelope, strategy);
```

> Username: awulkiew  
> Created_at: 2023-04-11 08:40:46 UTC  
> Updated_at: 2023-04-11 08:40:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162486713  

The indentation was changed. Was it intentional?

> Username: barendgehrels  
> Created_at: 2023-04-12 07:56:03 UTC  
> Updated_at: 2023-04-12 07:56:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163762770  

I see, no it was not intentional. :heavy_check_mark:


---

## Review 11 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-11 08:44:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378736763  

📁 include/boost/geometry/algorithms/detail/overlay/assign_parents.hpp

```diff
 325 |                     {
 334 |-                         ring_info_type& inner = ring_map[it->id];
 326 |+                         ring_info_type& inner = ring_map[item.id];
```

> Username: awulkiew  
> Created_at: 2023-04-11 08:44:01 UTC  
> Updated_at: 2023-04-11 08:57:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162490374  

Out of curiosity, why have you left `ring_info_type`? Not that I'm against it becasue I think that `auto` should be used carefully and only if it improves readability. I'm just curious.

> Username: barendgehrels  
> Created_at: 2023-04-12 07:59:27 UTC  
> Updated_at: 2023-04-12 07:59:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163766718  

Indeed using auto is delicate. For iterators it's perfect. If it saves type definitions, I'm also inclined to use it.  
In this case it would not save that type.


---

## Review 12 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-11 08:53:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378753771  

📁 include/boost/geometry/algorithms/detail/overlay/intersection_insert.hpp

```diff
 383 |+             {
 384 |+                 debug_follow(turn, turn.operations[0], index);
 385 |+             });
```

> Username: awulkiew  
> Created_at: 2023-04-11 08:53:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162501963  

The indentation is inconsistent with other calls. Besides we should probably mimic loops as it is correctly done in other places.

> Username: barendgehrels  
> Created_at: 2023-04-12 08:00:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163767984  

:heavy_check_mark:


---

## Review 13 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-11 09:02:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378771370  

📁 include/boost/geometry/algorithms/detail/partition.hpp

```diff
 611 |+         for (auto it = boost::begin(forward_range);
 612 |             it != boost::end(forward_range);
 613 |             ++it)
```

> Username: awulkiew  
> Created_at: 2023-04-11 09:02:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162513267  

Would it make sense to put the loop in one line? It should be lesser than 100 characters.

> Username: barendgehrels  
> Created_at: 2023-04-12 08:01:08 UTC  
> Updated_at: 2023-04-12 08:01:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163768732  

Yes it fits.  
:heavy_check_mark:


---

## Review 14 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-11 09:04:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378774783  

📁 include/boost/geometry/algorithms/detail/relate/linear_areal.hpp

```diff
 308 |-         range2_iterator qk_it = find_next_non_duplicated(boost::begin(range2),
 307 |+         auto qk_it = find_next_non_duplicated(boost::begin(range2),
 308 |                                                          range::pos(range2, q_seg_jk),
```

> Username: awulkiew  
> Created_at: 2023-04-11 09:04:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162515409  

Indentation should probably be changed as well.


---

## Review 15 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-11 09:04:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378775243  

📁 include/boost/geometry/algorithms/detail/relate/follow_helpers.hpp

```diff
 261 |-         point_iterator entry_it = std::find_if(m_other_entry_points.begin(),
 260 |+         auto entry_it = std::find_if(m_other_entry_points.begin(),
 261 |                                                m_other_entry_points.end(),
```

> Username: awulkiew  
> Created_at: 2023-04-11 09:04:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162515699  

Indentation should probably be changed as well.


---

## Review 16 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-11 09:06:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378779218  

📁 include/boost/geometry/algorithms/detail/relate/point_point.hpp

```diff
  70 |+     auto it = boost::begin(multi_point);
  71 |+     auto last = boost::end(multi_point);
  72 |+     for ( ; it != last ; ++it)
```

> Username: awulkiew  
> Created_at: 2023-04-11 09:06:58 UTC  
> Updated_at: 2023-04-11 09:06:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162518435  

These iterators could probably be put inside the loop. At least `it` because with `last`/`end` there could be a debate about performance.

> Username: barendgehrels  
> Created_at: 2023-04-12 08:04:11 UTC  
> Updated_at: 2023-04-12 08:04:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163772427  

:heavy_check_mark: fixed and renamed `last` to `end` as done elsewhere


---

## Review 17 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-11 09:10:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378784876  

📁 include/boost/geometry/algorithms/detail/relate/topology_check.hpp

```diff
 187 |-             typename boost::range_reference<MultiLinestring const>::type
 188 |-                 ls = *it;
 186 |+             auto ls = *it;
```

> Username: awulkiew  
> Created_at: 2023-04-11 09:10:11 UTC  
> Updated_at: 2023-04-11 09:47:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162522175  

This would cause a linestring to be copied. `auto const&` should probably be used here instead.  
  
It's an issue preventing me to approve the PR.

> Username: barendgehrels  
> Created_at: 2023-04-12 08:07:34 UTC  
> Updated_at: 2023-04-12 08:07:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163776159  

:+1: well spotted, thanks  
:heavy_check_mark: changed to `auto const&` and a few lines later (for points) as well.


---

## Review 18 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-11 09:33:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378829040  

📁 include/boost/geometry/algorithms/detail/within/within_no_turns.hpp

```diff
  71 |-               it != boost::end(geometry::interior_rings(geometry2)) ;
  72 |-               ++it )
  72 |+         auto&& rings2 = geometry::interior_rings(geometry2);
```

> Username: awulkiew  
> Created_at: 2023-04-11 09:33:44 UTC  
> Updated_at: 2023-04-11 09:36:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162552283  

AFAIU the intention is to represent const reference (even if non-true temporary), not forwarding/universal reference so `auto const&` would probably be more appropriate.

> Username: barendgehrels  
> Created_at: 2023-04-12 08:16:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163786247  

I copied this from https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/correct.hpp#L135  
but indeed, it's not const there.  
  
Changed to `auto const&`, indeed we use that more often


---

## Review 19 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-11 09:34:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378829553  

📁 include/boost/geometry/algorithms/detail/within/within_no_turns.hpp

```diff
 104 |-               it != boost::end(geometry::interior_rings(geometry2)) ;
 105 |-               ++it )
 108 |+         auto&& rings2 = geometry::interior_rings(geometry2);
```

> Username: awulkiew  
> Created_at: 2023-04-11 09:34:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162552642  

Same here.

> Username: barendgehrels  
> Created_at: 2023-04-12 08:17:21 UTC  
> Updated_at: 2023-04-12 08:17:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163787026  

:heavy_check_mark: fixed


---

## Review 20 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-11 09:34:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378830547  

📁 include/boost/geometry/algorithms/detail/within/within_no_turns.hpp

```diff
 120 |-                       it1 != boost::end(geometry::interior_rings(geometry1)) ;
 121 |-                       ++it1 )
 121 |+                 auto&& rings1 = geometry::interior_rings(geometry1);
```

> Username: awulkiew  
> Created_at: 2023-04-11 09:34:36 UTC  
> Updated_at: 2023-04-11 09:34:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162553415  

And here.

> Username: barendgehrels  
> Created_at: 2023-04-12 08:17:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163787598  

:heavy_check_mark: fixed


---

## Review 21 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-11 09:37:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378834754  

📁 include/boost/geometry/algorithms/for_each.hpp

```diff
 330 |-             rings = interior_rings(poly);
 331 |- 
 329 |+         auto&& rings = interior_rings(poly);
```

> Username: awulkiew  
> Created_at: 2023-04-11 09:37:06 UTC  
> Updated_at: 2023-04-11 09:37:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162556246  

Forwarding reference, are we planning to forward `rings`?

> Username: barendgehrels  
> Created_at: 2023-04-12 08:20:42 UTC  
> Updated_at: 2023-04-12 08:20:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163790836  

Here it is a non-const.  
So I changed it like done in `correct`.  
Maybe `auto&` is better in this case, changed it to that. I think that's OK here, @awulkiew ?

> Username: awulkiew  
> Created_at: 2023-04-12 18:01:52 UTC  
> Updated_at: 2023-04-12 18:01:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1164473754  

Right, if it's non-const then we have to use `auto&&`. If we use `auto&` the lifetime of a temporary which might be returned by `interior_rings()` will not be extended.

> Username: barendgehrels  
> Created_at: 2023-04-13 17:03:49 UTC  
> Updated_at: 2023-04-13 17:03:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1165810284  

thanks


---

## Review 22 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-11 09:39:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378838997  

📁 include/boost/geometry/io/svg/write.hpp

```diff
 169 |-             for (typename detail::interior_iterator<Polygon const>::type
 170 |-                     rit = boost::begin(rings); rit != boost::end(rings); ++rit)
 160 |+             auto&& rings = interior_rings(polygon);
```

> Username: awulkiew  
> Created_at: 2023-04-11 09:39:33 UTC  
> Updated_at: 2023-04-11 09:39:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162559146  

Forwarding reference.

> Username: barendgehrels  
> Created_at: 2023-04-12 08:18:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163788736  

:heavy_check_mark: fixed


---

## Review 23 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-11 09:40:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378841136  

📁 include/boost/geometry/algorithms/centroid.hpp

```diff
 261 |-         for (typename detail::interior_iterator<Polygon const>::type
 262 |-                 it = boost::begin(rings); it != boost::end(rings); ++it)
 258 |+         auto&& rings = interior_rings(poly);
```

> Username: awulkiew  
> Created_at: 2023-04-11 09:40:51 UTC  
> Updated_at: 2023-04-11 09:40:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162560712  

The intention here is to represent const reference, not forwarding reference. `auto const&` should probably be used here instead.

> Username: barendgehrels  
> Created_at: 2023-04-12 08:21:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163791784  

:heavy_check_mark: fixed


---

## Review 24 [Changes requested]

> Username: awulkiew  
> Created_at: 2023-04-11 09:44:04 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378846421  

That's a big PR. Thanks!  
I see some minor issues and one preventing me to approve it, i.e. unintended copy of a linestring. There is also unintended copy of a point but that's not that big of a problem.

---

## Review 25 [Approved]

> Username: vissarion  
> Created_at: 2023-04-11 10:09:12 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1378791896  

Thanks, LGTM in general with some (minor) comments.

📁 include/boost/geometry/algorithms/centroid.hpp

```diff
 261 |-         for (typename detail::interior_iterator<Polygon const>::type
 262 |-                 it = boost::begin(rings); it != boost::end(rings); ++it)
 258 |+         auto&& rings = interior_rings(poly);
```

> Username: vissarion  
> Created_at: 2023-04-11 09:13:46 UTC  
> Updated_at: 2023-04-11 10:09:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162526891  

Should we be more specific here using `auto const &` since rings are not modifiable?

> Username: barendgehrels  
> Created_at: 2023-04-12 08:21:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163792192  

:+1: :heavy_check_mark: fixed

---

📁 include/boost/geometry/algorithms/centroid.hpp

```diff
 262 |-                 it = boost::begin(rings); it != boost::end(rings); ++it)
 258 |+         auto&& rings = interior_rings(poly);
 259 |+         auto const end = boost::end(rings);
```

> Username: vissarion  
> Created_at: 2023-04-11 09:15:09 UTC  
> Updated_at: 2023-04-11 10:09:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162528813  

What is the reason of copying here and not just using it as `boost::end(rings)` inside for loop as you are doing below?

> Username: barendgehrels  
> Created_at: 2023-04-12 08:22:18 UTC  
> Updated_at: 2023-04-12 08:22:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163792602  

It's done elsewhere as well, for rings.

> Username: awulkiew  
> Created_at: 2023-04-12 18:04:06 UTC  
> Updated_at: 2023-04-12 18:04:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1164475879  

I'm not sure if this is the reason but the effect is that end will not be calculated at the end of each iteration.


📁 include/boost/geometry/algorithms/densify.hpp

```diff
  85 |-         iterator_t end = boost::end(rng);
  81 |+         auto it = boost::begin(rng);
  82 |+         auto end = boost::end(rng);
```

> Username: vissarion  
> Created_at: 2023-04-11 09:16:34 UTC  
> Updated_at: 2023-04-11 10:09:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162530898  

Is it better to declare it as `auto const` or even `auto const&` ?

> Username: barendgehrels  
> Created_at: 2023-04-12 08:23:04 UTC  
> Updated_at: 2023-04-12 09:51:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163793481  

~~You see it occasionally, but usually iterators are never declared const.~~  
I would not make them `auto const&` because it's not a reference (though it's syntactically correct)  
  
Edit: fixed, made `const` :heavy_check_mark:


📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 787 | 
 804 |-         for (++it; it != boost::end(range); ++it)
 788 |+         for (++it; it != std::cend(range); ++it)
```

> Username: vissarion  
> Created_at: 2023-04-11 09:36:24 UTC  
> Updated_at: 2023-04-11 10:09:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162555431  

Similar to @awulkiew's comment above on `std::cbegin`. Is there an intention to remove dependencies on `boost::begin` and `boost::end` from this file? If this is the case I think you could also remove the inclusion of the related header files in the beginning of this file.

> Username: barendgehrels  
> Created_at: 2023-04-12 08:23:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163793851  

:+1: :heavy_check_mark: reverted


📁 include/boost/geometry/algorithms/detail/distance/geometry_to_segment_or_box.hpp

```diff
 208 |-         seg_or_box_const_iterator it_min2 = it_min1;
 200 |+         auto pit_min = points_begin(geometry);
 201 |+         auto it_min1 = boost::const_begin(seg_or_box_points);
```

> Username: vissarion  
> Created_at: 2023-04-11 09:46:26 UTC  
> Updated_at: 2023-04-11 10:09:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162567260  

shouldn't this and the next one be `auto const` ?

> Username: barendgehrels  
> Created_at: 2023-04-12 08:23:47 UTC  
> Updated_at: 2023-04-12 09:56:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163794311  

~~See above, we don't have that habit for iterators~~  
  
~~Edit: fixed~~  
  
Edit2: rolled back, it was actually changed

> Username: awulkiew  
> Created_at: 2023-04-12 18:10:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1164481886  

Defining it as `auto const` will prevent us from changing the iterator e.g incrementg it. If that's ok then it could be `const` like in case of end/last. Think about a pointer to const (`const_iterator`) vs const pointer (`auto const`).

> Username: barendgehrels  
> Created_at: 2023-04-13 17:02:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1165809209  

Yes, I changed it where @vissarion requested this. I completely agree now.  
  
But specifically in this case, this iterator is meant to change. It is an iterator pointing to the minimum. So it cannot be made `const` here.

---

📁 include/boost/geometry/algorithms/detail/distance/geometry_to_segment_or_box.hpp

```diff
 303 | 
 321 |-         iterator_type it_min
 304 |+         auto it_min
```

> Username: vissarion  
> Created_at: 2023-04-11 09:50:24 UTC  
> Updated_at: 2023-04-11 10:09:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162571779  

should it be `auto const` ?

> Username: barendgehrels  
> Created_at: 2023-04-12 08:23:58 UTC  
> Updated_at: 2023-04-12 09:47:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163794553  

~~Same here~~  
edit: fixed :heavy_check_mark:


📁 include/boost/geometry/algorithms/detail/envelope/range_of_boxes.hpp

```diff
 265 |-                                                 boost::end(range_of_boxes),
 266 |-                                                 latitude_less<max_corner>());
 252 |+         auto it_min = std::min_element(boost::begin(range_of_boxes),
```

> Username: vissarion  
> Created_at: 2023-04-11 09:51:38 UTC  
> Updated_at: 2023-04-11 10:09:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162573068  

`auto const`?

> Username: barendgehrels  
> Created_at: 2023-04-12 08:24:11 UTC  
> Updated_at: 2023-04-12 09:44:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163794795  

~~Same here~~  
edit: fixed :heavy_check_mark:


📁 include/boost/geometry/algorithms/line_interpolate.hpp

```diff
  96 |-         iterator_t end = boost::end(range);
  94 |+         auto it = boost::begin(range);
  95 |+         auto end = boost::end(range);
```

> Username: vissarion  
> Created_at: 2023-04-11 10:07:21 UTC  
> Updated_at: 2023-04-11 10:09:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1162591033  

`auto const` ?

> Username: barendgehrels  
> Created_at: 2023-04-12 08:24:17 UTC  
> Updated_at: 2023-04-12 09:45:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1163794895  

~~Same here~~  
Edit: for `end` which is never changed, we actually did it more often. Changed here :heavy_check_mark:


---

## Comment 26

> Username: barendgehrels  
> Created_at: 2023-04-12 12:57:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#issuecomment-1505231233  

New version is pushed.  
Thanks for the thorough reviews!  
  
I'm still doing some checks, adding a unit test which catches those changes in range-based-for-loops (because apparently they did not exist yet for several algorithms), and catches copies.  
So it does not need to be merged yet.

---

## Review 27 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-14 12:56:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1385411413  

📁 include/boost/geometry/algorithms/for_each.hpp

```diff
 330 |-             rings = interior_rings(poly);
 331 |- 
 328 |+         auto& rings = interior_rings(poly);
```

> Username: awulkiew  
> Created_at: 2023-04-14 12:56:05 UTC  
> Updated_at: 2023-04-14 12:56:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1166803388  

```diff  
-        auto& rings = interior_rings(poly);  
+        auto&& rings = interior_rings(poly);  
```

> Username: barendgehrels  
> Created_at: 2023-04-16 12:37:01 UTC  
> Updated_at: 2023-04-16 12:37:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1167895428  

:heavy_check_mark: fixed, thanks


---

## Review 28 [Commented]

> Username: awulkiew  
> Created_at: 2023-04-14 13:04:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1385423997  

📁 include/boost/geometry/util/for_each_with_index.hpp

```diff
  35 |+ {
  36 |+     std::size_t index = 0;
  37 |+     for (auto it = std::begin(container); it != std::end(container); ++it, ++index)
```

> Username: awulkiew  
> Created_at: 2023-04-14 13:04:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1166811389  

Technically the type of index should be taken from Container/Range, e.g.: `boost::size_type<Container>::type` but I guess in practice `std::size_t` will be ok.  
  
Are we sure that this utility will not be used for geometries adapted to Boost.Range Concept?  
Would it make sense to use `boost::begin` and `boost::end` to be safe?

> Username: barendgehrels  
> Created_at: 2023-04-16 12:36:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1167895388  

:heavy_check_mark: fixed. While fixing it, I now also use Boost Range's size type


---

## Comment 29

> Username: barendgehrels  
> Created_at: 2023-04-16 12:51:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#issuecomment-1510369101  

Results are pushed again. The last push also changes some other interior_ring_type occurrences, fixes a bug (concept) and adds two missing include files.  
This is caught by the new unit test (still to be pushed).

---

## Comment 30

> Username: barendgehrels  
> Created_at: 2023-04-16 17:15:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#issuecomment-1510435418  

Second commit adds tests (and fixes related concept).  
Finally I'll have to add similar tests for (multi)linestring.  
It proves to be quite useful, several functions do not support these geometries

---

## Review 31 [Commented]

> Username: barendgehrels  
> Created_at: 2023-04-16 17:16:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1386998939  

📁 include/boost/geometry/io/wkt/write.hpp

```diff
 205 |-         auto const rings = interior_rings(poly);
 203 |+         auto const& exterior = exterior_ring(poly);
 204 |+         auto const& rings = interior_rings(poly);
```

> Username: barendgehrels  
> Created_at: 2023-04-16 17:16:21 UTC  
> Updated_at: 2023-04-16 17:16:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1167978982  

My bad, I changed this last December. We didn't have unit tests catching this.


---

## Review 32 [Commented]

> Username: barendgehrels  
> Created_at: 2023-04-16 17:24:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1129#pullrequestreview-1387000117  

📁 test/geometries/custom_non_copiable/cnc_container.hpp

```diff
  16 |+ // Define a custom class which is only movable and not copiable, and cannot be indexed.
  17 |+ // Its derivatives will be linestring, ring, multi*
  18 |+ // For readability purposes we use the "cnc" abbreviation as "custom_non_copiable"
```

> Username: barendgehrels  
> Created_at: 2023-04-16 17:24:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#discussion_r1167980322  

This class originates from `all_custom_geometries` but takes it a bit further, making it non-copyable.  
For test purposes it has to be indexable, but that's also customized (the library does not see that).  
  
Also the adaptations are more modern (requiring less typedefs, more readable)


---

## Comment 33

> Username: awulkiew  
> Created_at: 2023-04-18 20:26:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#issuecomment-1513754018  

Thanks!

---

## Comment 34

> Username: barendgehrels  
> Created_at: 2023-04-19 17:32:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1129#issuecomment-1515112059  

@vissarion many tests are added, I'll assume you are still OK. I will merge one of the coming days.

---
