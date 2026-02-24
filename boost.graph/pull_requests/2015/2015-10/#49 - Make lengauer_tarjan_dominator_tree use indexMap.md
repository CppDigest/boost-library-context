# #49 Make lengauer_tarjan_dominator_tree use indexMap [Merged]

> Username: jviiret  
> Created at: 2015-10-28 06:26:36 UTC  
> Updated at: 2016-05-10 06:01:32 UTC  
> Merged at: 2016-05-01 23:52:37 UTC  
> Closed at: 2016-05-01 23:52:37 UTC  
> Url: https://github.com/boostorg/graph/pull/49  

Pass the indexMap parameter through to the dominator_visitor and use it  
instead of get(vertex_index, g). This allows this algorithm to be used  
for graphs without a vertex_index property.  
  
Addresses bug #11742.

---
