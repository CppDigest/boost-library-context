# #32 Make it clearer that when tree_edge is called [Closed]

> Username: xuhdev  
> Created at: 2015-02-12 01:45:48 UTC  
> Updated at: 2015-07-21 19:08:33 UTC  
> Closed at: 2015-07-21 19:07:04 UTC  
> Url: https://github.com/boostorg/graph/pull/32  

tree_edge function is called before the target vertex is discovered, so users should be acknoledged that at this moment, the target's color is still white.  
  
---  
  
There may be a more proper way to fix the document, but I think the original version is confusing, as the target is actually still white.

---

## Comment 1

> Username: Belcourt  
> Created_at: 2015-07-21 17:05:32 UTC  
> Url: https://github.com/boostorg/graph/pull/32#issuecomment-123404345  

The Boost development model is to post PR's against the develop branch.  We only apply changes to master from the develop branch.  Please close this PR against master and submit against develop.

---

## Comment 2

> Username: xuhdev  
> Created_at: 2015-07-21 19:08:33 UTC  
> Url: https://github.com/boostorg/graph/pull/32#issuecomment-123446633  

OK, new PR made. Thanks

---
