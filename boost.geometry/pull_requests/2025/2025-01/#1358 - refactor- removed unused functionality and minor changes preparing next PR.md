# #1358 refactor: removed unused functionality and minor changes preparing next PR [Merged]

> Username: barendgehrels  
> Created at: 2025-01-01 22:22:41 UTC  
> Updated at: 2025-03-18 13:19:02 UTC  
> Merged at: 2025-01-10 17:42:45 UTC  
> Closed at: 2025-01-10 17:42:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1358  

This relatively small PR removes things and changes a few minor things.  
It prepares the next PR which will change traversal.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-01 22:25:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1358#pullrequestreview-2526936721  

📁 include/boost/geometry/algorithms/detail/overlay/enrich_intersection_points.hpp

```diff
 157 |-             // the same for two consecutive operations - but the conditions are changed
 158 |-             // and this should be verified again)
 159 |-             next_index = advance(next_index);
```

> Username: barendgehrels  
> Created_at: 2025-01-01 22:25:15 UTC  
> Updated_at: 2025-01-01 22:25:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1358#discussion_r1900469584  

This functionality was active - but it is long obsolete and now handled in another way (and will change in next PR)


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-01 22:25:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1358#pullrequestreview-2526936798  

📁 include/boost/geometry/algorithms/detail/overlay/enrich_intersection_points.hpp

```diff
 290 |-                        IncludePolicy const& include_policy)
 259 |+ template <typename Turns, typename IncludePolicy>
 260 |+ inline auto create_map(Turns const& turns, IncludePolicy const& include_policy)
```

> Username: barendgehrels  
> Created_at: 2025-01-01 22:25:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1358#discussion_r1900469655  

This is just a modernization.  
(background: next PR needs including discarded here - to verify traversal)


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-01 22:26:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1358#pullrequestreview-2526936828  

📁 include/boost/geometry/algorithms/detail/overlay/enrich_intersection_points.hpp

```diff
 441 |-         has_colocations = ! clusters.empty();
 442 |-     }
 443 |- 
```

> Username: barendgehrels  
> Created_at: 2025-01-01 22:26:15 UTC  
> Updated_at: 2025-01-01 22:33:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1358#discussion_r1900469704  

This should now be called before, by the caller.  
It is more convenient, it was different for buffer.  
And next PR will change the gather_cluster_properties anyway and pass a visitor.  
Buffer has another visitor.  
Therefore it is more convenient to move it out, it doesn't really belong here.


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-01 22:26:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1358#pullrequestreview-2526936866  

📁 include/boost/geometry/algorithms/detail/overlay/enrich_intersection_points.hpp

```diff
 444 |+         detail::overlay::cleanup_clusters(turns, clusters);
 445 |+         detail::overlay::colocate_clusters(clusters, turns);
 446 |+     }
```

> Username: barendgehrels  
> Created_at: 2025-01-01 22:26:40 UTC  
> Updated_at: 2025-01-01 22:36:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1358#discussion_r1900469799  

This is now called before `create_map` (it is independent of the map itself)


---

## Review 5 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-01 22:28:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1358#pullrequestreview-2526937038  

📁 test/robustness/overlay/buffer/recursive_polygons_buffer.cpp

```diff
  83 |- bool verify(std::string const& caseid, MultiPolygon const& mp, MultiPolygon const& buffer, Settings const& settings)
  82 |+ template <typename Geometry, typename Buffer>
  83 |+ bool verify_buffer(Geometry const& geometry, Buffer const& buffer, std::string& reason, bool check_validity)
```

> Username: barendgehrels  
> Created_at: 2025-01-01 22:28:09 UTC  
> Updated_at: 2025-01-01 22:28:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1358#discussion_r1900469942  

This is a stricter check in the test program, and gives more structured info.  
It still happens a lot (around `0.25%` of the cases is wrong)


---

## Review 6 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-01 22:29:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1358#pullrequestreview-2526937254  

📁 include/boost/geometry/algorithms/detail/buffer/buffer_policies.hpp

```diff
 157 | };
 158 | 
 173 |- struct buffer_less
```

> Username: barendgehrels  
> Created_at: 2025-01-01 22:29:52 UTC  
> Updated_at: 2025-01-01 22:29:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1358#discussion_r1900470050  

It was unused


---

## Review 7 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-01 22:30:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1358#pullrequestreview-2526937266  

📁 include/boost/geometry/algorithms/detail/overlay/handle_colocations.hpp

```diff
 366 | 
 367 |- 
 368 |- struct is_turn_index
```

> Username: barendgehrels  
> Created_at: 2025-01-01 22:30:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1358#discussion_r1900470061  

It was unused


---

## Review 8 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-01 22:30:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1358#pullrequestreview-2526937284  

📁 include/boost/geometry/algorithms/detail/overlay/less_by_segment_ratio.hpp

```diff
  44 |     std::size_t turn_index;
  45 |     std::size_t operation_index;
  46 |-     bool skip;
```

> Username: barendgehrels  
> Created_at: 2025-01-01 22:30:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1358#discussion_r1900470071  

It was unused


---

## Review 9 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-01 22:39:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1358#pullrequestreview-2526938394  

📁 include/boost/geometry/algorithms/detail/buffer/buffer_policies.hpp

```diff
  90 |-     void print(char const* /*header*/)
  91 |-     {
  92 |-     }
```

> Username: barendgehrels  
> Created_at: 2025-01-01 22:39:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1358#discussion_r1900471170  

These 3 print functions were long gone and are not called anymore


---

## Review 10 [Commented]

> Username: tinko92  
> Created_at: 2025-01-02 13:04:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1358#pullrequestreview-2527488015  

LGTM (one note in in-line comment).

📁 test/robustness/overlay/buffer/recursive_polygons_buffer.cpp

```diff
  89 | 
  93 |-     if (area_buf < area_mp)
  90 |+     if (area_buf < std::numeric_limits<double>::epsilon())
```

> Username: tinko92  
> Created_at: 2025-01-02 12:24:54 UTC  
> Updated_at: 2025-01-02 15:53:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1358#discussion_r1900828651  

I think this may be too narrow for many summations that are zero up to rounding error. Deriving a strict error bound would be overkill here but multiplying eps with (settings.field_size + settings.distance) * (settings.field_size + settings.distance) would be a more principled heuristic for using the relative epsilon [1] and I would probably also multiply with 2 to account for the area formula and multiply with a rough estimate for the potential number of points of area_mp (around settings.points_per_circle * 2, I think, for a buffer around two rectangles?) for the error of accumulating over the segments. That still leaves the error bound at a very small order of ~1e-10 for default settings if I didn't make an error estimating it, so I wouldn't expect any false positives from it.  
  
[1] https://floating-point-gui.de/errors/comparison/#dont-use-absolute-error-margins

> Username: barendgehrels  
> Created_at: 2025-01-03 19:09:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1358#discussion_r1902084868  

Thanks! Point noticed and bookmarked.  
  
I first wanted to follow your suggestions, but than I realized that it is trivial and better to check for `if (buffer.empty())` instead.


---

## Review 11 [Approved]

> Username: vissarion  
> Created_at: 2025-01-10 11:28:52 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1358#pullrequestreview-2542212097  

I do not deeply understand the code but to my understanding the changes are OK. Thanks!

---
