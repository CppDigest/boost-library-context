# #57 - transform_iterator end iterator with non-default-constructible unary function [Open]

> Username: Kojoley  
> Created at: 2020-05-24 23:00:32 UTC  
> Updated at: 2024-10-30 17:57:57 UTC  
> Url: https://github.com/boostorg/iterator/issues/57  

Currently one have to construct/copy a second copy of transformation function for an end iterator what is unwanted and seems to be omission of the library design. For example, Boost.Range solves it via wrapping user functor with `optional`, unfortunately.

---

## Comment 1

> Username: Lastique  
> Created at: 2024-10-30 17:57:56 UTC  
> Url: https://github.com/boostorg/iterator/issues/57#issuecomment-2447948435  

Omitting the function object for an end iterator seems like a useful optimization, but the problem is that the end iterator can be made a non-end iterator by decrementing, and then the `transform_iterator` is expected to work.  
  
I suppose, we could add a new factory function and create a specialized version of `transform_iterator` for an "end" iterator that will simply `abort` in runtime on increments, decrements and dereference. Unfortunately, this won't be caught at compile time as the respective operators need to be callable so that the iterator category is still correctly detected by the concepts.
