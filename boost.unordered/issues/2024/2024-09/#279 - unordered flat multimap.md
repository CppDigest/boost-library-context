# #279 - [FR] unordered flat multimap [Open]

> Username: psiha  
> Created at: 2024-09-02 15:09:10 UTC  
> Updated at: 2025-12-03 19:43:00 UTC  
> Url: https://github.com/boostorg/unordered/issues/279  

If you could also add this container version/combination? 😬

---

## Comment 1

> Username: joaquintides  
> Created at: 2024-09-03 15:58:13 UTC  
> Url: https://github.com/boostorg/unordered/issues/279#issuecomment-2326884951  

Hi,  
  
Open addressing in general, and the variation used by Boost.Unordered flat containers in particular, do not lend themselves easily to the implementation of multimaps (or multisets). One big problem (there may be more) is that multi(map|set)s, as specified by the standard, need store equivalent elements adjacently, so that, for instance, `equal_range(k)` can return a pair of iterators comprising the elements equivalent to `k` (and those alone). Honoring this requirement would force us to relocate non-`k` elements when a new `k`-element is inserted --not impossible, but it'd certainly be a rather non-trivial complication.  
  
I haven't ever seen an open-addressing multi(map|set) out there, are you aware of any example we can learn from?

---

## Comment 2

> Username: jonesmz  
> Created at: 2025-12-03 19:43:00 UTC  
> Url: https://github.com/boostorg/unordered/issues/279#issuecomment-3608545952  

A related topic would be a concurrent multimap, whether "flat" or "node" variety.  
  
I encountered a situation where i needed to have multiple keys that all pointed to the same item, in code that already uses `boost::concurrent_{flat,node}_map`. The approach that I would have wished i could use was a multimap, but I would have lost the nice API and built-in mutex handling of the boost container as a result.  
  
Would supporting a `boost::unordered_node_multimap` and `boost::concurrent_node_multimap` be feasible even if the open-addressing approach used by a hypothetical `boost::unordered_flat_multimap` would be infeasible?
