# #43 Make it clearer that when tree_edge is called [Closed]

> Username: xuhdev  
> Created at: 2015-07-21 19:08:07 UTC  
> Updated at: 2016-05-01 20:18:47 UTC  
> Closed at: 2016-05-01 20:18:47 UTC  
> Url: https://github.com/boostorg/graph/pull/43  

tree_edge function is called before the target vertex is discovered, so  
users should be acknoledged that at this moment, the target's color is  
still white.

---

## Comment 1

> Username: Belcourt  
> Created_at: 2016-05-01 20:18:47 UTC  
> Url: https://github.com/boostorg/graph/pull/43#issuecomment-216069373  

I don't find that wording to be clarifying so I'm closing this PR.  I understand your concern and will consider improving the documentation.

---
