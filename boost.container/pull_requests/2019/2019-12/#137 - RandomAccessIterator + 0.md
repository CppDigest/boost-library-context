# #137 RandomAccessIterator + 0 [Merged]

> Username: RobertLeahy  
> Created at: 2019-12-23 16:47:54 UTC  
> Updated at: 2020-01-14 15:25:29 UTC  
> Merged at: 2020-01-14 15:25:19 UTC  
> Closed at: 2020-01-14 15:25:19 UTC  
> Url: https://github.com/boostorg/container/pull/137  

Previously the iterators of `boost::container::deque` would assert when zero was added to them in at least the following situations:  
  
- The iterator was obtained by a call to `boost::container::deque::begin` and `boost::container::deque::empty` returns `true`  
- The iterator was default constructed  
  
This is inconsistent with the way in which the iterators of `boost::container::deque` have behaved historically and is also inconsistent with an understanding of iterators developed by analogy with pointers:  
  
- Adding zero to a null pointer is valid despite the fact the null pointer cannot be dereferenced  
- Adding zero to a pointer that is one past the end yields a pointer which is still one past the end and thus which has well defined semantics  
  
Fixed this issue and codified the expected behavior in unit tests.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2020-01-14 15:25:29 UTC  
> Url: https://github.com/boostorg/container/pull/137#issuecomment-574227515  

Many thanks for the fix!

---
