# #894 Optimize rtree nearest queries, refactor spatial and nearest queries. [Merged]

> Username: awulkiew  
> Created at: 2021-07-30 19:11:27 UTC  
> Updated at: 2021-09-22 18:26:10 UTC  
> Merged at: 2021-09-22 18:26:10 UTC  
> Closed at: 2021-09-22 18:26:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/894  

This is an enhancement addressing the issue https://github.com/boostorg/geometry/issues/867 and more.  
  
List of changes:  
- added minmax heap and double-ended priority queue,  
- sorting of branches in distance query replaced with priority queue,  
- sorting of neighbors in iterative distance query replaced with d-e priority queue,  
- branches are prioritized based on distance and rtree node's level to ensure that leafs are reached ASAP in cases when there are multiple nodes with the same distance to visit,  
- nodes are no longer visited with `apply_visitor`, queries are no longer visitors, instead the rtree node's level is tracked and nodes are converted to `internal_node` or `leaf` with `get` (this is a change needed for the future modifications of the rtree).

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2021-08-18 14:00:47 UTC  
> Updated_at: 2021-08-18 14:01:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/894#issuecomment-901140296  

This is a bit outside my scope, a review will therefore take more time. I plan to do it coming week.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2021-08-22 21:45:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/894#issuecomment-903334735  

@barendgehrels Thanks! Though I'd appreciate more if you looked into this issue: https://github.com/boostorg/geometry/issues/900 in particular the case with robustness enabled.

---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2021-09-21 11:53:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/894#pullrequestreview-759643661  

Thanks! Should the test case of https://github.com/boostorg/geometry/issues/867 be added in the test-suite together with a note on performance/issue?

---

## Comment 4

> Username: awulkiew  
> Created_at: 2021-09-22 10:40:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/894#issuecomment-924806862  

Thanks for the reviews!  
  
@vissarion I think it is not needed. Even if I added a test case we wouldn't detect potential performance regressions because we don't test that. Correctness is not the issue here and is covered by other tests. Besides the full test case from this issue has AFAIR tens of millions of linestrings stored in mapped memory file.

---
