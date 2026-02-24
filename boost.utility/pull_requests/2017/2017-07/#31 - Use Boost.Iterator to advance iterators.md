# #31 Use Boost.Iterator to advance iterators. [Merged]

> Username: Lastique  
> Created at: 2017-07-12 18:17:18 UTC  
> Updated at: 2017-07-13 18:04:23 UTC  
> Merged at: 2017-07-13 18:04:19 UTC  
> Closed at: 2017-07-13 18:04:19 UTC  
> Url: https://github.com/boostorg/utility/pull/31  

This is in continuation of https://github.com/boostorg/utility/pull/30.  
  
By using Boost.Iterator we rely on the separate traversal category instead of  
the standard iterator category to advance iterators efficiently. For instance,  
this allows to advance transform iterators over a random access sequence  
in constant time, despite that they are formally input iterators.  
  
Also, `std::reverse_iterator` formally requires at least bidirectional iterator  
as the underlying iterator type. Transform iterators from the example above  
don't qualify, so potentially `std::reverse_iterator` could fail to compile.

---

## Comment 1

> Username: morinmorin  
> Created_at: 2017-07-12 23:56:16 UTC  
> Url: https://github.com/boostorg/utility/pull/31#issuecomment-314930557  

LGTM.  
  
Can you add a test? (`iterator/test/advance_test.cpp`, which contains testcases for ReadableIterator, can be reused.)  
  
Thanks,  
Michel

---

## Review 2 [Commented]

> Username: morinmorin  
> Created_at: 2017-07-12 23:57:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/31#pullrequestreview-49650597  

📁 include/boost/next_prior.hpp

```diff
 105 |     {
 104 |-         std::advance(x, n);
 106 |+         boost::iterators::advance(x, n);
```

> Username: morinmorin  
> Created_at: 2017-07-12 23:57:21 UTC  
> Url: https://github.com/boostorg/utility/pull/31#discussion_r127101089  

FWIF, this can also be written as `boost::advance`.

> Username: Lastique  
> Created_at: 2017-07-13 08:59:49 UTC  
> Url: https://github.com/boostorg/utility/pull/31#discussion_r127163635  

Names in `boost` are mostly for backward compatibility, newer code should use `boost::iterators`.


---

## Comment 3

> Username: Lastique  
> Created_at: 2017-07-13 09:03:08 UTC  
> Url: https://github.com/boostorg/utility/pull/31#issuecomment-315018012  

> Can you add a test? (`iterator/test/advance_test.cpp`, which contains testcases for ReadableIterator, can be reused.)  
  
I thought about a test but it would compile with both the old and the new version of the code, and the end result would be the same. It's an optimization, so it's difficult to test.

---

## Comment 4

> Username: morinmorin  
> Created_at: 2017-07-13 10:36:42 UTC  
> Url: https://github.com/boostorg/utility/pull/31#issuecomment-315040793  

> Names in boost are mostly for backward compatibility, newer code should use `boost::iterators`.  
  
OK, agreed.  
  
> I thought about a test but it would compile with both the old and the new version of the code, and the end result would be the same.   
  
What I thought is that `next(bidirectional_readable_iter, -2)` doesn't work properly when used with `std::advance`. But I just realized that the documentation says the second argument should be non-negative. Sorry for the noise.

---
