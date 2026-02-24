# #30 Proof-of-concept `splice` operation [Closed]

> Username: sehe  
> Created at: 2017-09-23 09:47:39 UTC  
> Updated at: 2017-09-25 13:03:16 UTC  
> Closed at: 2017-09-25 13:03:16 UTC  
> Url: https://github.com/boostorg/property_tree/pull/30  

It's currently not possible to efficiently move nodes between ptrees.  
  
Context/discussion: https://stackoverflow.com/a/29563086/85371  
  
This PR is a **DRAFT**. It is meant to sollicit interest and quick review (is the idea "sane"? I do not believe any invariants are at risk, but maybe the core maintainers see more).  
  
If there's interest, I'll flesh it out in full.  
  
-----  
  
Multi Index splice overloads: http://www.boost.org/doc/libs/release/libs/multi_index/doc/reference/seq_indices.html#list_operations  
  
reviewer suggestion: Joaquín M López Muñoz

---

## Comment 1

> Username: joaquintides  
> Created_at: 2017-09-25 07:31:39 UTC  
> Url: https://github.com/boostorg/property_tree/pull/30#issuecomment-331799562  

I'm afraid this does not work as intended: Boost.MultiIndex `splice` operations do relink only when the source and destination indices are the same --in all other cases elements are inserted to and erased from one by one, copying along the way.

---

## Comment 2

> Username: sehe  
> Created_at: 2017-09-25 13:03:05 UTC  
> Url: https://github.com/boostorg/property_tree/pull/30#issuecomment-331873724  

@joaquintides Thanks for chiming in. That was easy :) I should have looked at the implementation for a bit (it was surprising to me, though, I didn't expect that behaviour just reading the documentation).

---
