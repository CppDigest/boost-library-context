# #1369 feat: major rewrite of traversal [Merged]

> Username: barendgehrels  
> Created at: 2025-01-29 21:05:19 UTC  
> Updated at: 2025-07-16 15:43:56 UTC  
> Merged at: 2025-04-25 17:47:35 UTC  
> Closed at: 2025-04-25 17:47:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369  

As per #1366   
  
This is a big PR. Current stats:  
` 85 files changed, 3955 insertions(+), 6767 deletions(-)`  
  
Fixes:  
* #1221   
* #1295   
* #1349   
  
  
Summary:  
* it started using Boost.Graph to determine biconnected components, instead of our own isolation detection  
* that's still there - but we can use it also for traversal  
* that needed some time but it is now in a good shape.  
  
Tests (as also reported earlier - but these are runs of today):  
* `random_bitset_grids`:   
     * develop: `iterations: 100000 errors: 8 time: 48.776`  
     * this pr: `iterations: 100000 errors: 0 time: 53.875` (better but still a bit slower, as reported in the discussion page)  
* `recursive_polygons_buffer`:  
    * develop: `geometries: 148743 errors: 307 type: d time: 8.806 (miter)`  
    * this pr: `geometries: 149509 errors: 111 type: d time: 7.817 (miter)`  
    * but with join `round` it is different:  
    * develop: `geometries: 147743 errors: 518 type: d time: 8.541 (round)`  
    * this pr: `geometries: 146474 errors: 762 type: d time: 7.875 (round)`  
    * I investigated some cases - they were perfect, these were (3 cases) false negatives from valid. We should fix them.   
* `robustness_recursive_polygons`  
    * this pr: `geometries: 1269839 errors: 9 type: d time: 86.551`  
    * develop: `geometries: 1270000 errors: 0 type: d time: 92.921` - so a bit of regression here, should be investigated  
  
Some things could not yet be fixed:  
* the behaviour of start turns (removing then when not necessary) is not yet OK. There is a "concept fix" included (not active)  
* the behaviour of get_turn_info is not always fine, especially where `block_q` is used. There is a "concept fix" included (not active)  
* the behaviour of segment ratio at arrival is not always fine, also here is a "concept fix" included  
* all these concept fixes should be worked out, as in this PR it's not fine everywhere. I can also omit it - but it is for your information  
* also, the buffer often considers a turn (of two pieces, say A and B) inside another piece (say C), and another (say: of A and C) as well (say: in B). This is conceptually not possible and should be fixed  
* and  there are false negatives for invalidity, as mentioned above

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2025-01-30 17:20:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#issuecomment-2625109283  

With respect to:  
> They were perfect, these were (3 cases) false negatives from valid  
  
So, in some cases, the previous version delivered one polygon for a cluster of UX/XU turns. The current delivers two, which makes sense, it's a touching point (a cluster). Though there is around 5.0e-16 between them - so if you consider it really close, it should be one polygon.  
  
When it is changed (it's relatively easy), the statistics are:  
`# geometries: 149315 errors: 221 type: d time: 8.063 (round)` (so, also better than in `develop` now)  
  
PostGIS: it finds both variants fine.  
  
SQL Server: it considers only the one with two polygons as valid. So clustered turn. The other is invalid.  
  
<img width="739" alt="image" src="https://github.com/user-attachments/assets/be3613f6-ed8f-4595-848a-d2fb8e6d5f42" />

---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2025-03-05 11:54:58 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1369#pullrequestreview-2621367126  

In general this is great, it simplifies things a lot and it seems that resolves several issues.  
I have a few inline comments.   
Moreover, have you tested with robust side strategy?

📁 include/boost/geometry/algorithms/detail/overlay/graph/is_target_operation.hpp

```diff
 136 |+ template <typename Operation, typename Turns>
 137 |+ bool is_better_collinear_for_intersection(Operation const& op, Operation const& other_op,
 138 |+         turn_operation_id const& toi, turn_operation_id const& other_toi, Turns const& turns)
```

> Username: vissarion  
> Created_at: 2025-02-17 15:25:24 UTC  
> Updated_at: 2025-03-05 11:54:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r1958417597  

I think indentation is inconsistent, one tab or two tabs or aligned with "(" in some cases.

> Username: barendgehrels  
> Created_at: 2025-04-19 08:27:27 UTC  
> Updated_at: 2025-04-19 08:27:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2051430447  

Historically I used 2 tabs - but I agree that later we (probably including myself) column-align with "("   
or use 1 or more tabs.  
See my comment on clang-format.  
I'll not change this particular occasion because it's there in many places.

> Username: barendgehrels  
> Created_at: 2025-04-19 08:59:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2051435492  

Looking further, in that same file I usually use 1 tab indeed. So I changed it.  
✅

---

📁 include/boost/geometry/algorithms/detail/overlay/graph/is_target_operation.hpp

```diff
 104 |+     if (op.enriched.ahead_side != other_op.enriched.ahead_side)
 105 |+     {
 106 |+         // If one of them goes left (1), this one is preferred above collinear or right (-1),
```

> Username: vissarion  
> Created_at: 2025-03-04 14:48:44 UTC  
> Updated_at: 2025-03-05 11:54:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r1979614161  

Is there a reasoning for that tie breaking or is it just arbitrary?

> Username: tinko92  
> Created_at: 2025-04-15 13:35:31 UTC  
> Updated_at: 2025-04-15 13:56:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2044560228  

If the correctness of the algorithm depends on this tie breaking logic, I think it would be strongly preferable to restate this as some topological predicate that is in strategies.

> Username: barendgehrels  
> Created_at: 2025-04-19 07:44:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2051423426  

Can we later (after merging) come back to this?  
I understand the point (a bit) but I would prefer not to make too many structural changes to this PR at this moment...  
  
To be clear, it is not an error in the logic, it is a "normal" situation. But it could be expressed in a different way indeed.

> Username: tinko92  
> Created_at: 2025-04-19 08:55:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2051434760  

I understand, thanks. This is resolved from my site then (not clicking "resolve" because the conversation involves vissarion as well).

> Username: barendgehrels  
> Created_at: 2025-04-19 09:00:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2051435773  

Added a follow-up as #1390


📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 443 |         }
 444 | 
 445 |+         // This fixes the fact that sometimes wrong ix or xi turns are generated.
```

> Username: vissarion  
> Created_at: 2025-03-04 13:07:47 UTC  
> Updated_at: 2025-03-05 11:54:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r1979396108  

It is possible to not generate them at all for the buffer case? In which sense they are wrong? Are they also wrong for intersection/diffrence and how is this handled?

> Username: barendgehrels  
> Created_at: 2025-03-05 17:52:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r1981897506  

Thanks for approval and all the comments!  
I will address them next week, or next-next.  
The question is good. In the meantime I forgot some details so I have recheck it.

> Username: barendgehrels  
> Created_at: 2025-04-19 07:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2051420845  

I just picked it up again with some delay.

> Username: barendgehrels  
> Created_at: 2025-04-19 08:57:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2051435298  

Created a follow up in #1389


📁 include/boost/geometry/algorithms/detail/overlay/graph/assign_clustered_counts.hpp

```diff
 319 |+     }
 320 |+ 
 321 |+     void assign_zone_counts(std::vector<connection_item<point_type>>& item_vector, std::vector<std::size_t> const& zone_counts, std::size_t rank_size)
```

> Username: vissarion  
> Created_at: 2025-03-04 13:27:52 UTC  
> Updated_at: 2025-03-05 11:54:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r1979433754  

this is probably a too long line

> Username: barendgehrels  
> Created_at: 2025-04-19 07:31:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2051421236  

✅

---

📁 include/boost/geometry/algorithms/detail/overlay/graph/assign_clustered_counts.hpp

```diff
 426 |+     // It should be possible to fix it in another way.
 427 |+     void change_reversed_operations(signed_size_type const cluster_id, cluster_info const& cluster,
 428 |+                     point_type const& point_turn, point_type const& point_origin)
```

> Username: vissarion  
> Created_at: 2025-03-04 13:33:48 UTC  
> Updated_at: 2025-03-05 11:54:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r1979442669  

indentation could be fixed here

> Username: barendgehrels  
> Created_at: 2025-04-19 07:32:22 UTC  
> Updated_at: 2025-04-19 07:32:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2051421375  

✅   
I now think, at a certain moment, we might go to `clang-format` if we get a satisfactory alignment consensus.


📁 include/boost/geometry/algorithms/detail/overlay/graph/assign_side_counts.hpp

```diff
  49 |+     if (OverlayType != overlay_buffer)
  50 |+     {
  51 |+         // Increase right-count for self-buffers. This should not be called for buffers
```

> Username: vissarion  
> Created_at: 2025-03-04 14:21:01 UTC  
> Updated_at: 2025-03-05 11:54:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r1979561786  

What is a self-buffer and what is the difference to a buffer?

> Username: barendgehrels  
> Created_at: 2025-03-05 17:53:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r1981898378  

It's an error, I meant: `self-intersections`

> Username: barendgehrels  
> Created_at: 2025-04-19 07:32:50 UTC  
> Updated_at: 2025-04-19 07:32:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2051421435  

✅


📁 include/boost/geometry/algorithms/detail/overlay/graph/detect_biconnected_components.hpp

```diff
  55 |+ 
  56 |+     // Reverse mapping. Every node (turn or cluster) has only ONE vertex,
  57 |+     // but there might be so called "extra" vertices, not associated with a node.
```

> Username: vissarion  
> Created_at: 2025-03-04 14:41:16 UTC  
> Updated_at: 2025-03-05 11:54:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r1979600349  

OK, if I understand well, you call nodes the points of the polygon (e.g. turns) and vertices the vertices of the graph. Then there are some "extra" vertices that are created for round-trips. Those are not associated with a node. *If that is correct*, I see some inconsistencies here and there. e.g. there is the term "extra node" that should be replaced with "extra vertex". Maybe I am missing something here.

> Username: barendgehrels  
> Created_at: 2025-04-19 08:49:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2051433677  

Yes, good point.  
  
Revisiting it, I'll try to explain:  
  
The node id of the turn:  
* if it is clustered, the negative cluster_id  
* if it is not clustered, the turn index  
* there can also be extra nodes for the round trip (>= turns.size())  
  
So need something more to indicate a round trip, for graph only which is not a turn. For this we use (or abuse) `from turn.size() and up, such that they have a unique positive id.`  
  
They (all these 3 kinds of nodes) are used in the `node_to_vertex_index` mapping.  
  
Therefore I think that `extra_node_id` is correctly named.  
  
The edge and vertex come from Boost.Graph  
  
Therefore I think that `vertex_index` is also correctly named.

---

📁 include/boost/geometry/algorithms/detail/overlay/graph/detect_biconnected_components.hpp

```diff
 172 |+         if (it_source == state.vertex_map.end() || it_target == state.vertex_map.end())
 173 |+         {
 174 |+             // THIS SHOULD BE AN ERROR
```

> Username: vissarion  
> Created_at: 2025-03-04 14:43:18 UTC  
> Updated_at: 2025-03-05 11:54:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r1979604257  

should it raise an exception or something?

> Username: barendgehrels  
> Created_at: 2025-04-19 07:41:17 UTC  
> Updated_at: 2025-04-19 07:41:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2051422914  

✅ we hardly throw - but here we could indeed. Added `logic_exception`


📁 include/boost/geometry/algorithms/detail/overlay/graph/graph_util.hpp

```diff
  25 |+ };
  26 |+ 
  27 |+ // It appears that in an undirected graph, the components for two edges are sometimes different.
```

> Username: vissarion  
> Created_at: 2025-03-04 14:46:09 UTC  
> Updated_at: 2025-03-05 11:54:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r1979609479  

Could be useful to list a unit test name here where this happens, to be easily reproduced in the future.

> Username: barendgehrels  
> Created_at: 2025-04-19 08:54:41 UTC  
> Updated_at: 2025-04-19 08:54:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2051434652  

Yes, it happens a lot.   
`*** 104 failures are detected in the test module "Test Program"`  
  
For example `case_recursive_boxes_99` and `case_recursive_boxes_93`  
I'll add it with the '93' example.  
✅


📁 include/boost/geometry/algorithms/detail/overlay/overlay.hpp

```diff
 130 |             }
 131 |- 
 131 |+ 
```

> Username: vissarion  
> Created_at: 2025-03-05 11:14:32 UTC  
> Updated_at: 2025-03-05 11:54:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r1981192588  

trailing whitespace

> Username: barendgehrels  
> Created_at: 2025-04-19 07:45:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2051423496  

✅


📁 include/boost/geometry/algorithms/detail/overlay/traverse.hpp

```diff
  73 |+                 Turns, Clusters,
  74 |+                 IntersectionStrategy
  75 |+             > trav(geometry1, geometry2, turns, clusters,
```

> Username: vissarion  
> Created_at: 2025-03-05 11:16:51 UTC  
> Updated_at: 2025-03-05 11:54:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r1981195593  

maybe `traverse_graph` is better than `trav`

> Username: barendgehrels  
> Created_at: 2025-04-19 07:53:09 UTC  
> Updated_at: 2025-04-19 07:53:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2051424701  

That confuses me a bit because the type is already called `traverse_graph`.   
But I agree of course that `trav` is not good.  
It will probably work but I prefer having something else. Maybe just `graph` or `traverser`.

> Username: barendgehrels  
> Created_at: 2025-04-19 08:05:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2051426695  

✅ using `traverser`


---

## Comment 3

> Username: tinko92  
> Created_at: 2025-03-05 15:43:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#issuecomment-2701333018  

I think this looks like a great set of changes. Due to the significant number of lines, I will need a lot of time to review it and hope to do it by ~mid April, I hope that works, assuming such a major change is not targetting the current release window.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2025-03-05 17:49:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#issuecomment-2701659959  

> In general this is great, it simplifies things a lot and it seems that resolves several issues. I have a few inline comments. Moreover, have you tested with robust side strategy?  
  
Yes, I tested with robust side strategy - especially in the parts of determining open space - but I don't see (big/any) difference. I did it halfway, when not all was ready. I can test again.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2025-03-05 17:50:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#issuecomment-2701661932  

> I think this looks like a great set of changes. Due to the significant number of lines, I will need a lot of time to review it and hope to do it by ~mid April, I hope that works, assuming such a major change is not targetting the current release window.  
  
Yes, that works fine, thanks. Indeed it's so major that we cannot target 1.88 anymore.

---

## Review 6 [Commented]

> Username: tinko92  
> Created_at: 2025-04-15 13:28:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1369#pullrequestreview-2768277576  

📁 include/boost/geometry/algorithms/detail/overlay/graph/assign_clustered_counts.hpp

```diff
 140 |+             geometry::copy_segment_point<Reverse1, Reverse2>(m_geometry1, m_geometry2,
 141 |+                 op.seg_id, from_offset--, point_from);
 142 |+         } while (approximately_equals(point_from, point_turn, tolerance) && from_offset > -max_iterations);
```

> Username: tinko92  
> Created_at: 2025-04-15 13:28:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2044543375  

I think, the library would strongly benefit from moving this kind of logic into strategies as a named predicate, even if there is initially only one choice and it will be the same for all coordinate systems for now.  
  
My understanding is that this involves two, possibly not precisely constructed points. Whether this needs a tolerance or not depends on a combination of coordinate system and coordinate type, e.g. cartesian + rational type would not need to be approximately but geographic + rational or cartesian + double would. The tolerance also depends on the coordinate systems even with the same coordinate type.

> Username: barendgehrels  
> Created_at: 2025-04-19 08:20:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2051429155  

Thanks for the comment.  
I agree that it could be enhanced, but I cannot do it now.  
Also, this part is moved - but it was already in the existing code base (be it [elsewhere](https://github.com/boostorg/geometry/blob/c08975488559cad0660d201b61360821f7814a47/include/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp#L333)).  
Captured in #1387

> Username: barendgehrels  
> Created_at: 2025-04-19 08:23:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2051429785  

✅


---

## Review 7 [Commented]

> Username: tinko92  
> Created_at: 2025-04-15 13:56:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1369#pullrequestreview-2768286525  

I posted some initial comments. I think this PR is a big improvement and helps simplifying algorithm, which I think is important.  
  
I think, it would be better still, if computations involving distances (calls to comparable distance) and coordinates (approximately_equals) that are added in this PR (I am aware that they replace code that did this too) could be restated as predicates and moved out of geometry/algorithms entirely.  
  
The more the algorithms-code operates on an exact model (be written as if strategies were exact), the more tractable it will be to narrow down remaining and future issues to much simpler functions in strategies.

📁 include/boost/geometry/algorithms/detail/overlay/graph/assign_clustered_counts.hpp

```diff
 384 |+             // If it is positive (on the left side), it is a spike.
 385 |+             auto const dm = get_distance_measure(current.opposite_point, current.point, next.point, m_intersection_strategy);
 386 |+             if (dm.measure <= 0)
```

> Username: tinko92  
> Created_at: 2025-04-15 13:30:50 UTC  
> Updated_at: 2025-04-15 13:56:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2044548708  

Related to https://github.com/boostorg/geometry/pull/1369/files#r2044543375, can this be a named predicate in strategies?

> Username: barendgehrels  
> Created_at: 2025-04-19 08:22:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2051429670  

✅ captured as #1388


📁 include/boost/geometry/algorithms/detail/overlay/graph/select_edge.hpp

```diff
  78 |+                 op.seg_id, offset, point);
  79 |+             ++offset;
  80 |+         } while(approximately_equals(point, turn_point, tolerance) && offset < 10);
```

> Username: tinko92  
> Created_at: 2025-04-15 13:37:17 UTC  
> Updated_at: 2025-04-15 13:56:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#discussion_r2044565213  

Same note as https://github.com/boostorg/geometry/pull/1369/files#r2044543375 .


---

## Comment 8

> Username: barendgehrels  
> Created_at: 2025-04-19 09:47:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#issuecomment-2816634557  

> Moreover, have you tested with robust side strategy?  
  
I just compared it and it improves the behaviour!  
  
I use the same random seed (`42`) for all tests (and testing more cases than described in the PR description).  
  
`recursive_polygons_buffer`:  
* with `side_by_triangle`: `geometries: 603809 errors: 506 type: d time: 67.352`  
* with `side_robust`: `geometries: 610519 errors: 417 type: d time: 62.962`  
  
-> **better** result and **also faster**  
  
`recursive_polygons`:  
* with `side_by_triangle`: `geometries: 1270000 errors: 2 type: d time: 85.285`  
* with `side_robust`: `geometries: 1270000 errors: 2 type: d time: 77.708`  
  
-> same result and **faster**  
  
I didn't do the test `random_bitset_grids` because it does not give errors (and is based on integer)  
  
**I'm quite happy to see this!** Well done @tinko92 !  
  
However, I propose to not change the default strategy in this PR. I'll create another PR for it.

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2025-04-19 10:21:22 UTC  
> Updated_at: 2025-04-19 10:22:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#issuecomment-2816647437  

> tested with robust side strategy?  
  
On `develop`, same tests:  
  
`recursive_polygons_buffer`:  
* with `side_by_triangle`: `geometries: 559976 errors: 1243 type: d time: 70.776`  
* with `side_robust`: `geometries: 542196 errors: 1520 type: d time: 58.12`  
  
`recursive_polygons`:  
* with `side_by_triangle`: `geometries: 1270000 errors: 0 type: d time: 91.907`  
* with `side_robust`: `geometries: 1270000 errors: 0 type: d time: 78.439`  
  
So here it is actually the other way round (in terms of errors, for buffer). That is probably the reason that we didn't make it the default earlier (though I forgot the details a bit).  
  
The small regression in recursive polygons was already known (pr description).  
  
Anyway - this test kind of proves that the major rewrite of traversal is fine and that it works well with the side_robust strategy

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2025-04-19 11:05:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#issuecomment-2816660479  

The workflow should be updated (#1392). Then all tests should pass.  
  
I created some follow up tasks regarding strategies.  
  
@tinko92 do you then have further comments?  
  
I can process some of them next weekend, but in May I will probably be too busy to have time to work on it.

---

## Review 11 [Approved]

> Username: tinko92  
> Created_at: 2025-04-19 15:35:13 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1369#pullrequestreview-2780006075  

I have no further comments, thanks for addressing them so quickly and for this big improvement.

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2025-04-19 17:35:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#issuecomment-2816795568  

> I have no further comments, thanks for addressing them so quickly and for this big improvement.  
  
Thanks! I'll merge as soon as the CI is updated.

---

## Comment 13

> Username: barendgehrels  
> Created_at: 2025-04-19 17:41:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#issuecomment-2816797808  

Also fixes: #1382

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2025-04-25 06:37:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1369#issuecomment-2829503235  

Rebased, squashed and pushed to trigger new CI  
I also changed commit date.

---
