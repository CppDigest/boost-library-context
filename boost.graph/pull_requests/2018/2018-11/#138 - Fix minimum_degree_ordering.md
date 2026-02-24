# #138  Fix minimum_degree_ordering [Merged]

> Username: CromwellEnage  
> Created at: 2018-11-23 06:20:46 UTC  
> Updated at: 2019-02-05 20:25:32 UTC  
> Merged at: 2019-01-22 17:59:53 UTC  
> Closed at: 2019-01-22 17:59:53 UTC  
> Url: https://github.com/boostorg/graph/pull/138  

Handle degenerate case where input graph contains no edges.

---

## Review 1 [Commented]

> Username: jeremy-murphy  
> Created_at: 2018-12-08 10:38:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/138#pullrequestreview-182940263  

📁 test/Jamfile.v2

```diff
 159 |+     graph_test_no_msvc14
 160 |+     ;
 161 |+ 
```

> Username: jeremy-murphy  
> Created_at: 2018-12-08 10:38:35 UTC  
> Updated_at: 2018-12-16 06:19:40 UTC  
> Url: https://github.com/boostorg/graph/pull/138#discussion_r240000960  

Why all these other changes that seem unrelated to `minimum_degree_ordering`? Maybe better to put them in a different PR?

> Username: jeremy-murphy  
> Created_at: 2018-12-08 10:53:14 UTC  
> Updated_at: 2018-12-16 06:19:40 UTC  
> Url: https://github.com/boostorg/graph/pull/138#discussion_r240001248  

Oh, I get it now, you merged your parameter fix in here too. Looks like we should get that one merged asap.


---

## Comment 2

> Username: jzmaddock  
> Created_at: 2018-12-15 15:51:32 UTC  
> Url: https://github.com/boostorg/graph/pull/138#issuecomment-447577810  

Can you rebase this to develop please?

---

## Comment 3

> Username: CromwellEnage  
> Created_at: 2018-12-15 16:36:36 UTC  
> Url: https://github.com/boostorg/graph/pull/138#issuecomment-447580829  

Rebased.

---

## Review 4 [Commented]

> Username: jeremy-murphy  
> Created_at: 2018-12-16 03:02:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/138#pullrequestreview-185373332  

📁 include/boost/graph/minimum_degree_ordering.hpp

```diff
 323 |+           if (0 < d) has_no_edges = false;
 324 |           degreelists.push(*v);
 325 |         }
```

> Username: jeremy-murphy  
> Created_at: 2018-12-16 03:02:49 UTC  
> Updated_at: 2018-12-16 06:19:40 UTC  
> Url: https://github.com/boostorg/graph/pull/138#discussion_r241966463  

For simplicity, I would prefer the assignment `d = ...` to happen before the `put` rather than inline, and I would move the declaration of `d` inside that loop too. It's just a preference for scope and simplicity, nothing serious.

> Username: CromwellEnage  
> Created_at: 2018-12-16 06:20:52 UTC  
> Url: https://github.com/boostorg/graph/pull/138#discussion_r241969234  

Done.


---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2018-12-16 11:54:00 UTC  
> Url: https://github.com/boostorg/graph/pull/138#issuecomment-447637029  

Looks good to me!

---

## Comment 6

> Username: CromwellEnage  
> Created_at: 2019-01-21 21:10:53 UTC  
> Url: https://github.com/boostorg/graph/pull/138#issuecomment-456199826  

@jzmaddock, your verdict?

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2019-01-22 17:59:47 UTC  
> Url: https://github.com/boostorg/graph/pull/138#issuecomment-456499028  

Yep, all good I think, merging.

---
