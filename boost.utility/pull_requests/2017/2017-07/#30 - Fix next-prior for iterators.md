# #30 Fix next/prior for iterators [Merged]

> Username: Lastique  
> Created at: 2017-07-09 00:12:49 UTC  
> Updated at: 2017-07-09 17:34:50 UTC  
> Merged at: 2017-07-09 10:41:04 UTC  
> Closed at: 2017-07-09 10:41:04 UTC  
> Url: https://github.com/boostorg/utility/pull/30  

This PR reworks handling of iterators by `boost::next/prior`. The changes are summarized below:  
  
- Previously the implementation tested for presence of arithmetic operators first and fell back to iterator-specific code using `std::advance`. This change reverses this order: the incremented/decremented type is tested if it's an iterator first and if not it is further tested for operators.  
- The iterator detection relies on the nested `iterator_category` type presence in class-type iterators. This is not a bullet-proof assumption and it may not hold if the type is not defined in the iterator but `std::iterator_traits` is specialized for this iterator type. This case is not supported by the implementation and will fail to compile. C++17 `std::iterator_traits` are needed in order to support it, but for now we have no macro to detect it. A patch for Boost.Config is needed.  
- Simplified `boost::prior` for iterators to avoid possible integer overflow on distance negation.  
- This PR also includes additional tests from #29 to verify that the new implementation works for reverse iterators.

---

## Review 1 [Commented]

> Username: morinmorin  
> Created_at: 2017-07-09 14:04:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/30#pullrequestreview-48778168  

📁 include/boost/next_prior.hpp

```diff
 151 |+     {
 152 |+         // Avoid negating n to sidestep possible integer overflow
 153 |+         std::reverse_iterator< T > rx(x);
```

> Username: morinmorin  
> Created_at: 2017-07-09 14:04:18 UTC  
> Url: https://github.com/boostorg/utility/pull/30#discussion_r126305584  

The missing feature of `boost::next/prior` is tag dispatch based on Boost's IteratorTraversalCategory rather than Standard's IteratorCategory. The use of `std::reverse_iterator` forbids calling `boost::prior` with iterators like  
```  
auto twice = [](int x) { return x + x; };  
  
std::vector<int> ints1 = ...;  
auto it1 = boost::make_transform_iterator(ints1.begin(), twice);  
// it1 is RandomAccessTraversalIterator in Boost's IteratorTraversalCategory.  
// However, it is just InputIterator in Standard's IteratorCategory, because  
// its reference type is not a real reference.  
  
std::list<int> ints2 = ...;  
auto it2 = boost::make_transform_iterator(ints2.begin(), twice);  
// it2 is BidirectionalTraversalIterator in Boost's IteratorTraversalCategory.  
// However, it is just InputIterator in Standard's IteratorCategory, because  
// its reference type is not a real reference.  
```  
since they are just InputIterator. IMHO, supporting Boost's IteratorTraversalCategory is more important than preventing overflow.

> Username: Lastique  
> Created_at: 2017-07-09 14:39:55 UTC  
> Url: https://github.com/boostorg/utility/pull/30#discussion_r126306795  

I don't want to trade integer overflow for additional functionality. I suppose, we could switch to `boost::reverse_iterator`, but I would prefer to avoid adding a dependency on Boost.Iterator. In practice, I think `std::reverse_iterator` should work in the case you presented, unless it actively tests for the underlying iterator category.

> Username: Lastique  
> Created_at: 2017-07-09 15:37:58 UTC  
> Url: https://github.com/boostorg/utility/pull/30#discussion_r126308173  

Also, since `boost::reverse_iterator` already depends on `boost::prior`, we can't use it now. See https://github.com/boostorg/iterator/pull/26.

> Username: morinmorin  
> Created_at: 2017-07-09 16:17:33 UTC  
> Url: https://github.com/boostorg/utility/pull/30#discussion_r126309031  

> I don't want to trade integer overflow for additional functionality.  
  
I thought that the type of the second parameter `n` of `boost::next/prior` is iterator's `differece_type` (as in `std::next/prev`) and so integer overflow rarely happens. Or do you have the following situation in mind?  
- The type of `n` is an unsigned integer type; or  
- `sizeof(n) > sizeof(differece_type)`.  
  
> In practice, I think std::reverse_iterator should work in the case you presented, unless it actively tests for the underlying iterator category.  
  
Right.   
  
> Also, since boost::reverse_iterator already depends on boost::prior, we can't use it now. See boostorg/iterator#26.  
  
I see. Thanks for making PR to Boost.Iterator.

> Username: morinmorin  
> Created_at: 2017-07-09 16:24:47 UTC  
> Url: https://github.com/boostorg/utility/pull/30#discussion_r126309174  

> I suppose, we could switch to boost::reverse_iterator, but I would prefer to avoid adding a dependency on Boost.Iterator.   
  
I understand the concern about the dependency on Boost.Iterator...  
But if the dependency is acceptable, then I would recommend to use `boost::advance` rather than `std::advance`. With `std::advance`, `boost::next(it1, 1000)` (where `it1` is RandomAccessTraversal Readable Iterator) increments `it1` 1000 times, since `it1` is treated as InputIterator. With `boost::advance`, `boost::next(it1, 1000)` just performs `it1 + 1000`.

> Username: Lastique  
> Created_at: 2017-07-09 16:28:29 UTC  
> Url: https://github.com/boostorg/utility/pull/30#discussion_r126309243  

`n` is not required to be `differece_type` and can have any type (including UDT or unsigned integral types) and potentially have larger range than `differece_type`. `std::advance` has a separate template parameter for distance, so all these cases should work as long as it doesn't cause overflows in the iterator itself.

> Username: morinmorin  
> Created_at: 2017-07-09 16:32:27 UTC  
> Updated_at: 2017-07-09 16:33:40 UTC  
> Url: https://github.com/boostorg/utility/pull/30#discussion_r126309326  

OK, thanks for the explanation.   
  
> std::advance has a separate template parameter for distance  
  
FWIF, `std2::advance` (in Range TS) does not have a separate template parameter.

> Username: Lastique  
> Created_at: 2017-07-09 16:48:05 UTC  
> Url: https://github.com/boostorg/utility/pull/30#discussion_r126309649  

> I would recommend to use `boost::advance` rather than `std::advance`.  
  
I understand. Unfortunately, `boost::advance` uses `difference_type` for the distance argument, so this would be a breaking change. Looks like it'll have to be re-implemented.

> Username: morinmorin  
> Created_at: 2017-07-09 16:51:35 UTC  
> Url: https://github.com/boostorg/utility/pull/30#discussion_r126309724  

> std::advance has a separate template parameter for distance  
  
For RandomAccessIterator, doesn't `std::advance` call `operator+=(difference_type n)` eventually? (which means that having a separate template parameter makes less sense at least for RandomAccessIterator.)

> Username: Lastique  
> Created_at: 2017-07-09 16:54:20 UTC  
> Url: https://github.com/boostorg/utility/pull/30#discussion_r126309773  

`std::advance` calls `operator+=`, yes. Whether the operator requires `difference_type` or allows something else depends on the iterator.

> Username: morinmorin  
> Created_at: 2017-07-09 17:34:50 UTC  
> Url: https://github.com/boostorg/utility/pull/30#discussion_r126310635  

> Whether the operator requires `difference_type` or allows something else depends on the iterator.  
  
OK, understood.  
  
> Unfortunately, `boost::advance` uses `difference_type` for the distance argument  
  
I implemented it in that way, because I followed the way of `std::next/prev` and `std2::advance`. I prefer the current implementation, but I'm not stuck to it; you can submit a PR to change the implementation.   
(Disclaimer: I'm not a maintainer of Boost.Iterator.)


---
