# #422 enrich_intersection_points: fix to_index0/to_index1 confusion [Merged]

> Username: MaxKellermann  
> Created at: 2017-09-08 13:22:14 UTC  
> Updated at: 2018-01-22 22:19:19 UTC  
> Merged at: 2017-09-08 16:39:05 UTC  
> Closed at: 2017-09-08 16:39:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/422  

Found by Coverity:  
  
 "pointless_expression: The expression to_index1 >= 0 && to_index1 >=  
 0 does not accomplish anything because it evaluates to either of its  
 identical operands, to_index1 >= 0."

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2017-09-08 16:39:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/422#issuecomment-328153864  

Thanks a lot, this was indeed wrong. And the fix is right. Can be merged - let's do it immediately.

---
