# #421 examples: C++11: Some use of range-based for loops [Merged]

> Username: murraycu  
> Created at: 2025-02-16 10:50:59 UTC  
> Updated at: 2025-03-12 10:07:33 UTC  
> Merged at: 2025-03-12 10:07:32 UTC  
> Closed at: 2025-03-12 10:07:33 UTC  
> Url: https://github.com/boostorg/graph/pull/421  



---

## Review 1 [Approved]

> Username: jeremy-murphy  
> Created_at: 2025-02-20 09:36:05 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/graph/pull/421#pullrequestreview-2629238441  

📁 example/topo-sort1.cpp

```diff
  38 |-     int n = 1;
  39 |-     for (auto i = topo_order.begin(); i != topo_order.end(); ++i, ++n)
  40 |-         std::cout << tasks[*i] << std::endl;
```

> Username: jeremy-murphy  
> Created_at: 2025-02-20 09:35:34 UTC  
> Updated_at: 2025-02-20 09:36:05 UTC  
> Url: https://github.com/boostorg/graph/pull/421#discussion_r1963187295  

`n` was not even used?

> Username: murraycu  
> Created_at: 2025-03-09 17:23:34 UTC  
> Url: https://github.com/boostorg/graph/pull/421#discussion_r1986375191  

Apparently not.


---

## Review 2 [Changes requested]

> Username: jeremy-murphy  
> Created_at: 2025-02-20 09:36:58 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/graph/pull/421#pullrequestreview-2629244820  

📁 example/cuthill_mckee_ordering.cpp

```diff
  60 |-     for (int i = 0; i < 14; ++i)
  61 |-         add_edge(edges[i].first, edges[i].second, G);
  60 |+     for (const auto& edge : edges)
```

> Username: jeremy-murphy  
> Created_at: 2025-02-20 09:36:52 UTC  
> Updated_at: 2025-02-20 09:36:58 UTC  
> Url: https://github.com/boostorg/graph/pull/421#discussion_r1963192287  

Sorry, you made one west const here.

> Username: murraycu  
> Created_at: 2025-03-09 17:22:07 UTC  
> Url: https://github.com/boostorg/graph/pull/421#discussion_r1986374969  

Thanks. Done.


---

## Review 3 [Approved]

> Username: jeremy-murphy  
> Created_at: 2025-03-12 10:07:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/graph/pull/421#pullrequestreview-2677815060  

Great, thanks for another modernization!

---
