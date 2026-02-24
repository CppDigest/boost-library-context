# #331 Don't look for disjoint-sets representatives in link [Merged]

> Username: mglisse  
> Created at: 2023-03-19 09:19:50 UTC  
> Updated at: 2023-09-12 03:30:54 UTC  
> Merged at: 2023-09-12 03:30:51 UTC  
> Closed at: 2023-09-12 03:30:51 UTC  
> Url: https://github.com/boostorg/graph/pull/331  

The arguments are already required to be the representative of their class. If we do not know the representatives, we are supposed to call union_set instead, which first finds them.  
Fix #327. In my application (very similar to minimum spanning tree), this saves about 5% of the total running time, which is small but not negligible. That was with full path compression, I think the gains are larger with path halving, where a second consecutive call is non-trivial.  
In example/incremental_components.cpp, there is a DS with vertex type size_t, but link is called on plain int, which can cause some conversion warnings. After the patch, in debug mode, it may become a warning about comparing the 2 types. Depending on your compiler and options, it may thus give a new warning where there was none, but in my opinion that's independent from this PR.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2023-09-12 03:30:54 UTC  
> Url: https://github.com/boostorg/graph/pull/331#issuecomment-1714909601  

Thanks for improving this!

---
