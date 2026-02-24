# #922 [distance] Add support for DG/GC in distance(). [Merged]

> Username: awulkiew  
> Created at: 2021-10-11 11:56:01 UTC  
> Updated at: 2021-10-17 18:35:33 UTC  
> Merged at: 2021-10-17 18:35:33 UTC  
> Closed at: 2021-10-17 18:35:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/922  



---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2021-10-13 09:54:57 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/922#pullrequestreview-778340964  

📁 include/boost/geometry/algorithms/detail/distance/geometry_collection.hpp

```diff
 141 |+ 
 142 |+             // The smallest possible distance found, end searching.
 143 |+             if (! is_first && result <= zero)
```

> Username: vissarion  
> Created_at: 2021-10-13 09:45:45 UTC  
> Updated_at: 2021-10-13 09:54:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/922#discussion_r727888275  

is it possible that `result` is negative?

> Username: awulkiew  
> Created_at: 2021-10-14 08:43:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/922#discussion_r728766367  

Technically it is, a (potentially user-defined) strategy can return any value. Possible reason may be an error or floating-point issue.


📁 include/boost/geometry/algorithms/detail/visit.hpp

```diff
 315 |+ //   with std::visit but different than std::ranges::for_each. It's done this way
 316 |+ //   because visit_breadth_first is also specialized for static and dynamic geometries
 317 |+ //   which and references for them has to be propagated like that. If this is not
```

> Username: vissarion  
> Created_at: 2021-10-13 09:54:18 UTC  
> Updated_at: 2021-10-13 09:54:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/922#discussion_r727895304  

it seems like a typo


---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2021-10-13 11:55:45 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/922#pullrequestreview-778466030  

📁 include/boost/geometry/algorithms/detail/distance/geometry_collection.hpp

```diff
 104 |+         geometry::detail::expand_by_epsilon(b2);
 105 |+ 
 106 |+         for (auto it = rtree.qbegin(index::nearest(b2, rtree.size())) ; it != rtree_qend ; ++it)
```

> Username: barendgehrels  
> Created_at: 2021-10-13 11:55:04 UTC  
> Updated_at: 2021-10-13 11:55:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/922#discussion_r727982209  

Minor, several lines are too long, I guess we still try to limit width up to 80 chars?

> Username: awulkiew  
> Created_at: 2021-10-14 08:40:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/922#discussion_r728763975  

We increased max to 100 some time ago. From [Guidelines-for-Developers](https://github.com/boostorg/geometry/wiki/Guidelines-for-Developers):  
> - The preferred line length is 80 characters, with maximum length of 100.  
>      - The limit is relaxed for very long string literals (e.g. Well-Known Text with data used in tests and examples).


---
