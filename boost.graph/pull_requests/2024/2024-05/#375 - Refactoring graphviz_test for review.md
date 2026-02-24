# #375 Refactoring graphviz_test for review [Merged]

> Username: sehe  
> Created at: 2024-05-03 03:34:23 UTC  
> Updated at: 2024-05-09 05:00:01 UTC  
> Merged at: 2024-05-09 05:00:01 UTC  
> Closed at: 2024-05-09 05:00:01 UTC  
> Url: https://github.com/boostorg/graph/pull/375  

(note this PR builds on #374)  
  
The code was mostly fine (except for unhygienic `using namespace` in  
places), but it was hard to see what was covered.  
  
I've seperated fixtures (sample input + expected output), and models into separate namespaces.  
  
This simplifies invocations of the ComparisonDriver (test_graph).

---

## Comment 1

> Username: sehe  
> Created_at: 2024-05-08 13:01:14 UTC  
> Url: https://github.com/boostorg/graph/pull/375#issuecomment-2100526458  

Updated for review comments https://github.com/boostorg/graph/issues/364#issuecomment-2100506381

---

## Review 2 [Commented]

> Username: jeremy-murphy  
> Created_at: 2024-05-09 04:56:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/375#pullrequestreview-2047143832  

📁 test/graphviz_test.cpp

```diff
  29 |+ typedef double Weight;
  30 |+ typedef std::map< node_t, Mass > expected_masses_t;
  31 |+ typedef std::map< edge_t, Weight > expected_weights_t;
```

> Username: jeremy-murphy  
> Created_at: 2024-05-09 04:56:54 UTC  
> Updated_at: 2024-05-09 04:56:55 UTC  
> Url: https://github.com/boostorg/graph/pull/375#discussion_r1594963406  

Personally, I think the type names should be more like `node_mass_map_t`, `edge_weight_map_t` and the variables should be named `expected_mass`, etc, but I'm not going to hold up the review over it.  :)


---
