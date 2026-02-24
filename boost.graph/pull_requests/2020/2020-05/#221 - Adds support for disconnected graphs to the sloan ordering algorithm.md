# #221 Adds support for disconnected graphs to the sloan ordering algorithm [Open]

> Username: bergfi  
> Created at: 2020-05-28 10:39:27 UTC  
> Updated at: 2020-05-28 10:39:27 UTC  
> Url: https://github.com/boostorg/graph/pull/221  

The sloan ordering algorithm before this patch could not handle disconnected graphs. We ran into this problem with the development of LTSmin and made this patch. This was already two years ago, but I figured it could still be valuable to someone else.  
  
I am in no means an expert in the algorithm. The changes seem logical, but it would be good if someone with a better understanding of the algorithm would look at it.  
  
The included test is a simple one, but fails before this patch: only one connected component is visited and node "3" is not considered at all. With the patch, both connected components are considered and the right ordering is achieved.  
  
Please let me know if I need to change anything.

---
