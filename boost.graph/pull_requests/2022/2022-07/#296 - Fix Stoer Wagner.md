# #296 Fix Stoer Wagner [Merged]

> Username: sebrockm  
> Created at: 2022-07-11 21:50:44 UTC  
> Updated at: 2022-07-12 07:13:25 UTC  
> Merged at: 2022-07-12 06:02:27 UTC  
> Closed at: 2022-07-12 06:02:27 UTC  
> Url: https://github.com/boostorg/graph/pull/296  

This PR fixes #286.  
  
It uses the non-degression test of #294 provided by @etiennedeg to verify the correctness.  
  
The fix is performed by simply rolling back the change introduced by commit https://github.com/boostorg/graph/commit/393c072c186f6711d1537225dc116ea1528140ac (identified by @etiennedeg).  
More precisely, only the changes made in stoer_wagner_min_cut.hpp were rolled back, all other changes (in particular, maximum_adjacency_search.hpp) are untouched by this PR.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2022-07-12 06:02:08 UTC  
> Url: https://github.com/boostorg/graph/pull/296#issuecomment-1181350003  

Huge thanks you both, @sebrockm and @etiennedeg !  
  
I love this example of the open-source community collaboration.

---
