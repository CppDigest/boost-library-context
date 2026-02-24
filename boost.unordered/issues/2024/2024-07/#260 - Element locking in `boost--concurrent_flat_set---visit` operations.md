# #260 - Element locking in `boost::concurrent_flat_set::*visit` operations [Closed]

> Username: joaquintides  
> Created at: 2024-07-08 08:45:05 UTC  
> Updated at: 2024-07-18 08:15:32 UTC  
> Closed at: 2024-07-18 08:15:32 UTC  
> Url: https://github.com/boostorg/unordered/issues/260  

As keys in a set are treated as const, `boost::concurrent_flat_set::visit(f)` (and all `*_or_visit` operations)  
are const member functions and behave exactly as their `cvisit` counterparts:  
  
* `f` is passed a const reference.  
* Access to the element is shared-locked.  
  
In some cases, though, we may want to have const, exclusive-locked access, such as for instance if the element has a `mutable` data member we'd like to modify. This could be in principle served by a non-const overloads of `boost::concurrent_flat_set::visit(f)` and other operations implying visitation.

---

## Comment 1

> Username: joaquintides  
> Created at: 2024-07-18 08:15:32 UTC  
> Url: https://github.com/boostorg/unordered/issues/260#issuecomment-2235903978  

Addressed in #265.
