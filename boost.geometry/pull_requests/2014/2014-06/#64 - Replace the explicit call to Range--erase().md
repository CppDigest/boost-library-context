# #64 Replace the explicit call to Range::erase() [Merged]

> Username: awulkiew  
> Created at: 2014-06-18 17:49:02 UTC  
> Updated at: 2014-06-21 10:31:58 UTC  
> Merged at: 2014-06-19 18:46:27 UTC  
> Closed at: 2014-06-19 18:46:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/64  

This call is replaced by a call to a free function range::erase() implemented using std::copy() and range::resize() which uses geometry::traits::resize<>.  
  
This PR also adds convenient range::resize() range::push_back() and range::clear() using mutable range traits.  
  
However we could think about rewriting this algorithm to not use erase at all. Is it required to remove elements from the beginning of the Range or could spikes/duplicates be handled by the removal of elements from the end?  
  
And an optimization is probably possible:  
1. first search for the first non-spike point,  
2. remove some number of elements from the beginning

---

## Comment 1

> Username: mkaravel  
> Created_at: 2014-06-18 20:30:09 UTC  
> Updated_at: 2014-06-19 18:45:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/64#discussion_r13938292  

@awulkiew I think we put the const after the type. So static bool const?

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-06-18 20:33:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/64#issuecomment-46490185  

+1 for this PR

---

## Comment 3

> Username: awulkiew  
> Created_at: 2014-06-19 10:36:48 UTC  
> Updated_at: 2014-06-19 10:44:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/64#issuecomment-46545693  

Ok, so I added tests and C++11-style erase(). If there were any problems with the ambiguity caused by the equality of the immutable and mutable iterators type we could use something like:  
enable_if&lt;!is_same&lt;range_iterator&lt;...>, range_iterator&lt;const...>>::value>  
Let me know which changes I should made before the merge.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2014-06-19 17:35:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/64#issuecomment-46591874  

Thanks for adding the unit tests. It can be merged right away. I prefer avoiding enable_if if not necessary, so if it compiles fine for all compilers including C++11, please leave it as it is.

---
