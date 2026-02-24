# #411 examples: C++11: Use auto [Merged]

> Username: murraycu  
> Created at: 2024-12-25 13:26:42 UTC  
> Updated at: 2025-02-03 06:05:52 UTC  
> Merged at: 2025-02-03 06:05:52 UTC  
> Closed at: 2025-02-03 06:05:52 UTC  
> Url: https://github.com/boostorg/graph/pull/411  

This is a small step - part of a larger old pull request to modernize the examples: https://github.com/boostorg/graph/pull/81

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2024-12-30 07:36:08 UTC  
> Updated_at: 2024-12-30 07:36:26 UTC  
> Url: https://github.com/boostorg/graph/pull/411#issuecomment-2565123859  

Can you check why it's failing?

---

## Review 2 [Commented]

> Username: jeremy-murphy  
> Created_at: 2025-01-01 22:23:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/411#pullrequestreview-2526936535  

📁 include/boost/graph/bc_clustering.hpp

```diff
 136 |-             = *boost::first_max_element(edges_iters.first, edges_iters.second, cmp);
 131 |+         auto edges_iters = edges(g);
 132 |+         auto e = *max_element(edges_iters.first, edges_iters.second, cmp);
```

> Username: jeremy-murphy  
> Created_at: 2025-01-01 22:23:34 UTC  
> Url: https://github.com/boostorg/graph/pull/411#discussion_r1900469307  

I haven't checked the whole diff, but this one change that doesn't fit with the stated purpose of the pull request is broken.  :)

> Username: murraycu  
> Created_at: 2025-01-06 12:04:18 UTC  
> Url: https://github.com/boostorg/graph/pull/411#discussion_r1904072373  

Thanks. Resolving the conflicts was awkward. I'll mark this as Ready for Review again when I've checked more carefully.


---

## Review 3 [Changes requested]

> Username: jeremy-murphy  
> Created_at: 2025-01-23 10:01:13 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/graph/pull/411#pullrequestreview-2569354691  

Thanks for an important modernization that shows users that Boost.Graph can be used with C++11!  
Just a few small changes I would like before merging it.

📁 example/boost_web_graph.cpp

```diff
 210 |     std::vector< size_type > dfs_distances(num_vertices(g), 0);
 211 |+ 
 212 |+     using NameMap = property_map< Graph, vertex_name_t >::type;
```

> Username: jeremy-murphy  
> Created_at: 2025-01-23 09:43:12 UTC  
> Updated_at: 2025-01-23 10:01:13 UTC  
> Url: https://github.com/boostorg/graph/pull/411#discussion_r1926671267  

Why is this change added?

> Username: murraycu  
> Created_at: 2025-02-02 14:41:07 UTC  
> Updated_at: 2025-02-02 14:41:08 UTC  
> Url: https://github.com/boostorg/graph/pull/411#discussion_r1938507528  

No idea. Removed. Thanks.

> Username: murraycu  
> Created_at: 2025-02-02 15:04:34 UTC  
> Updated_at: 2025-02-02 15:04:58 UTC  
> Url: https://github.com/boostorg/graph/pull/411#discussion_r1938512492  

Actually, this was just moved down from higher up in this file (and changed to a using rather than a typedef). It is needed.


📁 example/cycle_ratio_example.cpp

```diff
  71 |     cout << "Critical cycle:\n";
  75 |-     for (ccReal_t::iterator itr = cc.begin(); itr != cc.end(); ++itr)
  72 |+     for (const auto& edge : cc)
```

> Username: jeremy-murphy  
> Created_at: 2025-01-23 09:46:32 UTC  
> Updated_at: 2025-01-23 10:01:13 UTC  
> Url: https://github.com/boostorg/graph/pull/411#discussion_r1926676527  

Btw, here and elsewhere, please use East const.

> Username: murraycu  
> Created_at: 2025-02-02 14:53:07 UTC  
> Updated_at: 2025-02-02 14:53:08 UTC  
> Url: https://github.com/boostorg/graph/pull/411#discussion_r1938510338  

Done, here and in other changes in this PR, though existing code use west const.  
  
I do prefer east const, so maybe we should change it throughout to be consistent, separately?

> Username: jeremy-murphy  
> Created_at: 2025-02-03 06:02:52 UTC  
> Url: https://github.com/boostorg/graph/pull/411#discussion_r1938821900  

Good question. Although I believe firmly in East const, I try not to be militant about it and I think most of the time it just doesn't matter, so I'll be happy to introduce it in new code and when things change but not sure if I'll enact a sweeping change of the repo for it.


📁 example/edge-function.cpp

```diff
 117 |     boost::tie(i, end) = vertices(g);
 118 |     // Find yow.h
 119 |+     typedef property_map< graph_type, vertex_name_t >::type name_map_t;
```

> Username: jeremy-murphy  
> Created_at: 2025-01-23 09:48:41 UTC  
> Updated_at: 2025-01-23 10:01:13 UTC  
> Url: https://github.com/boostorg/graph/pull/411#discussion_r1926679762  

Please use the modern form, `using NAME = ...`.

> Username: murraycu  
> Created_at: 2025-02-02 14:45:17 UTC  
> Url: https://github.com/boostorg/graph/pull/411#discussion_r1938508333  

Sure, done, though this was actually just moving the line down from above.


📁 example/filtered_graph_edge_range.cpp

```diff
  45 |-         Graph;
  44 |+             property< edge_weight_t, int > >
  45 |+               Graph;
```

> Username: jeremy-murphy  
> Created_at: 2025-01-23 09:52:58 UTC  
> Updated_at: 2025-01-23 10:01:13 UTC  
> Url: https://github.com/boostorg/graph/pull/411#discussion_r1926686109  

I know it's just white space, but please try to remove this unnecessary change.

> Username: murraycu  
> Created_at: 2025-02-02 14:48:23 UTC  
> Url: https://github.com/boostorg/graph/pull/411#discussion_r1938509129  

Done.  
  
This was presumably a result of me running clang-format via "git format-patch HEAD^".


---
