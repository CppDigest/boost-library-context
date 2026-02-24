# #1248 fix: set turns in closed clusters as non traversable [Merged]

> Username: barendgehrels  
> Created at: 2024-02-25 10:36:34 UTC  
> Updated at: 2024-04-01 11:29:13 UTC  
> Merged at: 2024-02-28 16:34:05 UTC  
> Closed at: 2024-02-28 16:34:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248  



---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-02-25 10:37:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1248#pullrequestreview-1899648960  

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 902 |+                 for (auto index : cluster.second.turn_indices)
 903 |+                 {
 904 |+                     m_turns[index].is_turn_traversable = false;
```

> Username: barendgehrels  
> Created_at: 2024-02-25 10:37:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1501792189  

This is the main fix. Especially for linestrings, with flat end, there were many turns, completely closed, but still marked as traversable. This could cause the buffer fail (no or partial output)


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2024-02-25 10:39:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1248#pullrequestreview-1899649135  

📁 include/boost/geometry/algorithms/detail/overlay/cluster_info.hpp

```diff
  32 |+     //! Number of spikes, where a segment goes to the cluster point
  33 |+     //! and leaves immediately in the opposite direction.
  34 |+     std::size_t spike_count{0};
```

> Username: barendgehrels  
> Created_at: 2024-02-25 10:39:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1501792400  

This (and related code) is also necessary, to have a way out from a cluster which is closed, but where still is a way in and out. This is also related to floating point issues - the part between the spike legs might be indetectable


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2024-02-25 10:40:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1248#pullrequestreview-1899649421  

📁 include/boost/geometry/algorithms/detail/overlay/discard_duplicate_turns.hpp

```diff
 143 |+                             // or one is clustered and the other is not.
 144 |+                             // This is not corresponding.
 145 |+                             continue;
```

> Username: barendgehrels  
> Created_at: 2024-02-25 10:40:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1501792756  

This was earlier not possible. But now that clusters are detected before, the start turn and its corresponding turn should be located on the same cluster.

> Username: barendgehrels  
> Created_at: 2024-02-25 10:41:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1501792931  

We might be able to delete the `corresponding_turn` functions because of this.  
Will check that, but if possible, it will be a different PR

> Username: barendgehrels  
> Created_at: 2024-02-25 12:54:21 UTC  
> Updated_at: 2024-02-25 12:54:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1501815431  

It cannot be deleted, but it might be simplified


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2024-02-25 10:42:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1248#pullrequestreview-1899649719  

📁 include/boost/geometry/algorithms/detail/overlay/traversal.hpp

```diff
 605 |+             : to_start ? 4
 606 |+             : next_in_same_cluster ? 2
 607 |+             : 3
```

> Username: barendgehrels  
> Created_at: 2024-02-25 10:42:43 UTC  
> Updated_at: 2024-02-25 10:42:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1501793048  

The values just define the ordering. There is now 1 extra, lowest ordering: spike out

> Username: vissarion  
> Created_at: 2024-02-28 09:29:15 UTC  
> Updated_at: 2024-02-28 09:49:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1505631178  

Is there a comment that explain this orderings?

> Username: barendgehrels  
> Created_at: 2024-02-28 16:21:32 UTC  
> Updated_at: 2024-02-28 16:21:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1506237054  

✅ the comment was a bit above, but I didn't update it. Thanks.   
Comment is moved to function description, and a short comment here.


---

## Review 5 [Commented]

> Username: barendgehrels  
> Created_at: 2024-02-25 10:43:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1248#pullrequestreview-1899649810  

📁 test/algorithms/buffer/buffer_variable_width.cpp

```diff
 180 |+              {6.0, 4.5, 1.5}, 429.831);
 181 |+     test_buffer("case4", "MULTILINESTRING((-18.00000000002045652536253328435122966766357421875 10.4999999997948929575386500800959765911102294921875,-2.99999999998012700785920969792641699314117431640625 10.4999999997948929575386500800959765911102294921875),(-2.99999999998012700785920969792641699314117431640625 10.4999999997948929575386500800959765911102294921875,18.00000000002045652536253328435122966766357421875 10.4999999997948947338954894803464412689208984375),(-2.99999999998012700785920969792641699314117431640625 10.4999999997948929575386500800959765911102294921875,17.99999999995061017443731543608009815216064453125 -4.5000000001943778471513724070973694324493408203125))",
 182 |+              {6.0, 4.5, 1.5}, 423.195);
```

> Username: barendgehrels  
> Created_at: 2024-02-25 10:43:28 UTC  
> Updated_at: 2024-02-25 10:43:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1501793135  

Cases 3 and 4 were failing before the fixes


---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2024-02-25 12:02:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1248#pullrequestreview-1899662744  

📁 include/boost/geometry/algorithms/detail/overlay/enrich_intersection_points.hpp

```diff
 404 |+     bool has_colocations = false;
 405 |+ 
 406 |+     if (BOOST_GEOMETRY_CONDITION (! is_buffer))
```

> Username: awulkiew  
> Created_at: 2024-02-25 12:02:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1501805940  

`BOOST_GEOMETRY_CONSTEXPR` could be used instead.

> Username: barendgehrels  
> Created_at: 2024-02-25 12:52:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1501815074  

Sure, I'll change it

> Username: barendgehrels  
> Created_at: 2024-02-25 12:57:41 UTC  
> Updated_at: 2024-02-25 12:57:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1501816139  

As soon as your PR is merged ;-)

> Username: awulkiew  
> Created_at: 2024-02-25 19:20:45 UTC  
> Updated_at: 2024-02-25 19:20:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1501880869  

Ok, but in case you wasn't aware, this macro is already in develop:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/util/constexpr.hpp

> Username: barendgehrels  
> Created_at: 2024-02-26 20:37:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1503268189  

👍 right! Will update on Wednesday

> Username: barendgehrels  
> Created_at: 2024-02-28 16:22:03 UTC  
> Updated_at: 2024-02-28 16:22:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1506237813  

✅ this one is updated, it is a new block and has an else branch


---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2024-02-25 12:03:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1248#pullrequestreview-1899662940  

📁 include/boost/geometry/algorithms/detail/overlay/handle_colocations.hpp

```diff
 358 |         for (auto const& toi : kv.second)
 360 |-         {
 359 |+     {
```

> Username: awulkiew  
> Created_at: 2024-02-25 12:03:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1501806156  

probably wrong indent

> Username: barendgehrels  
> Created_at: 2024-02-28 16:22:14 UTC  
> Updated_at: 2024-02-28 16:22:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1506238083  

✅


---

## Review 8 [Commented]

> Username: awulkiew  
> Created_at: 2024-02-25 12:10:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1248#pullrequestreview-1899664450  

📁 include/boost/geometry/algorithms/detail/overlay/enrich_intersection_points.hpp

```diff
 418 |+                 Reverse2,
 419 |+                 OverlayType
 420 |+             >(clusters, turns, target_operation,
```

> Username: awulkiew  
> Created_at: 2024-02-25 12:10:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1501807579  

`target_operation` is known at compile-time. It could be passed as template parameter and then checked with `if BOOST_GEOMETRY_CONSTEXPR` inside `gather_cluster_properties`. But a different PR would probably be better for this.

> Username: barendgehrels  
> Created_at: 2024-02-28 16:22:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1506238693  

Will do it in another PR indeed. Not today.


---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2024-02-25 12:11:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1248#pullrequestreview-1899664698  

📁 include/boost/geometry/algorithms/detail/overlay/handle_colocations.hpp

```diff
 491 |             if (set_startable
 478 |-                     && for_operation == operation_union && cinfo.open_count == 0)
 492 |+                 && for_operation == operation_union
```

> Username: awulkiew  
> Created_at: 2024-02-25 12:11:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1501807817  

Here is the condition for `if constexpr`. Or is it not always the case that `for_operation` is known at compile-time?

> Username: barendgehrels  
> Created_at: 2024-02-25 12:52:59 UTC  
> Updated_at: 2024-02-25 12:53:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1501815156  

Yes, it is known. I'll update.

> Username: barendgehrels  
> Created_at: 2024-02-28 16:23:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1506240675  

I didn't update this one, and the ones below.  
Because `for_operation` is passed as a normal parameter.  
This needs more refactoring and it is better to separate that.


---

## Review 10 [Commented]

> Username: awulkiew  
> Created_at: 2024-02-25 12:14:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1248#pullrequestreview-1899665211  

📁 include/boost/geometry/algorithms/detail/overlay/traversal.hpp

```diff
 795 |         }
 796 | 
 797 |+         if (is_union && cinfo.open_count == 0 && cinfo.spike_count > 0)
```

> Username: awulkiew  
> Created_at: 2024-02-25 12:14:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1501808246  

`is_union` is `static const` so I guess it can cause warnings.

> Username: barendgehrels  
> Created_at: 2024-02-25 12:53:22 UTC  
> Updated_at: 2024-02-25 12:53:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1501815228  

indeed, I'll update.  
Thanks for your quick review.

> Username: barendgehrels  
> Created_at: 2024-02-28 16:26:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1506243929  

Similarly here: not updated. Here it was not passed, but it is used in many places like this in this source file. And they should all be updated.   
I guess, because of the combination of conditions, there are no warnings (not sure though). In other places it was combined as well, in this source.  
So also this should go to another PR.


---

## Comment 11

> Username: awulkiew  
> Created_at: 2024-02-25 12:21:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#issuecomment-1962919110  

Thanks for the PR and the detailed explanation of changes. :)

---

## Review 12 [Approved]

> Username: vissarion  
> Created_at: 2024-02-28 09:49:48 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1248#pullrequestreview-1905736130  

Thanks, LGTM in general.

📁 test/algorithms/buffer/buffer_variable_width.cpp

```diff
 156 |+             polygon_t
 157 |+         >(mls,
 158 |+             std::back_inserter(result),
```

> Username: vissarion  
> Created_at: 2024-02-28 09:33:44 UTC  
> Updated_at: 2024-02-28 09:49:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1505641310  

indentation issue here.

> Username: barendgehrels  
> Created_at: 2024-02-28 11:23:15 UTC  
> Updated_at: 2024-02-28 11:23:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1505792604  

✅

---

📁 test/algorithms/buffer/buffer_variable_width.cpp

```diff
  47 |+                                             OutputRange &output_range) const
  48 |+     {
  49 |+         const auto result =
```

> Username: vissarion  
> Created_at: 2024-02-28 09:40:41 UTC  
> Updated_at: 2024-02-28 09:49:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1505651096  

Maybe `auto const` which seems that is more common as a style in BG?   
Also why not `auto const&`?

> Username: barendgehrels  
> Created_at: 2024-02-28 11:15:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1505783923  

Yes it is. But on my office it's not, so I sometimes don't switch at the right moment...  
Thanks.  
BTW, in this case it should not be `&` because it's an assignment from a function result. (Though, technically, it's in line with C++ specs)

> Username: barendgehrels  
> Created_at: 2024-02-28 11:23:33 UTC  
> Updated_at: 2024-02-28 11:23:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1505792932  

✅


📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 895 |+             offsetted_rings, offsetted_rings, m_strategy);
 896 |+ 
 897 |+         for (const auto& cluster : m_clusters)
```

> Username: vissarion  
> Created_at: 2024-02-28 09:46:31 UTC  
> Updated_at: 2024-02-28 09:49:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1505662139  

`auto const&`?

> Username: barendgehrels  
> Created_at: 2024-02-28 11:23:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1505793332  

✅

---

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 900 |+             {
 901 |+                 // If the cluster is completely closed, mark it as not traversable.
 902 |+                 for (auto index : cluster.second.turn_indices)
```

> Username: vissarion  
> Created_at: 2024-02-28 09:47:57 UTC  
> Updated_at: 2024-02-28 09:49:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1505664207  

what about `auto const&` here?

> Username: barendgehrels  
> Created_at: 2024-02-28 11:16:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1505784638  

Indeed. Will fix later today. Same for your similar comments, thanks.

> Username: barendgehrels  
> Created_at: 2024-02-28 11:24:56 UTC  
> Updated_at: 2024-02-28 11:24:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1505794510  

✅

---

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 913 |+         {
 914 |+             bool is_traversable = false;
 915 |+             for (auto index : cluster.second.turn_indices)
```

> Username: vissarion  
> Created_at: 2024-02-28 09:48:42 UTC  
> Updated_at: 2024-02-28 09:49:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1505665285  

similarly, what about using `auto const&` here?

> Username: barendgehrels  
> Created_at: 2024-02-28 11:25:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1505794658  

✅

---

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 925 |+             if (is_traversable)
 926 |+             {
 927 |+                 for (auto index : cluster.second.turn_indices)
```

> Username: vissarion  
> Created_at: 2024-02-28 09:48:55 UTC  
> Updated_at: 2024-02-28 09:49:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1505665618  

same here

> Username: barendgehrels  
> Created_at: 2024-02-28 11:25:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1248#discussion_r1505794787  

✅


---
