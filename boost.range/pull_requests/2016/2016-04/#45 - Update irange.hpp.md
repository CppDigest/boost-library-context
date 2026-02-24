# #45 Update irange.hpp [Merged]

> Username: mateuszlewko  
> Created at: 2016-04-09 17:54:46 UTC  
> Updated at: 2017-11-15 20:10:22 UTC  
> Merged at: 2017-11-15 19:52:42 UTC  
> Closed at: 2017-11-15 19:52:42 UTC  
> Url: https://github.com/boostorg/range/pull/45  

Added: irange(int last), irange(int first, int last)  
They are useful for some quick iteration.   
  
irange(int last) -> when you need to iterate over range from 0 to last - 1, for example each index of string.size()  
  
irange(int, int)- > for auto casting, for example size_t to int. Passing size_t as argument won't implicitly cast to Integer so it produces an error but it's safe.

---

## Review 1 [Commented]

> Username: lakshayg  
> Created_at: 2017-10-30 17:29:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/range/pull/45#pullrequestreview-72917174  

📁 include/boost/range/irange.hpp

```diff
 219 |+         return integer_range<Integer>(first, last);
 220 |+     }
 221 |+ 
```

> Username: lakshayg  
> Created_at: 2017-10-30 17:22:25 UTC  
> Updated_at: 2017-10-30 17:29:56 UTC  
> Url: https://github.com/boostorg/range/pull/45#discussion_r147774014  

Lines 214-220 aren't actually required. The case when first and last are integers is handled by the method on line 224.

---

📁 include/boost/range/irange.hpp

```diff
 210 |+         BOOST_ASSERT( 0 <= last );
 211 |+         return integer_range<Integer>(0, last);
 212 |+     }
```

> Username: lakshayg  
> Created_at: 2017-10-30 17:27:58 UTC  
> Updated_at: 2017-10-30 17:29:56 UTC  
> Url: https://github.com/boostorg/range/pull/45#discussion_r147775670  

Instead of repeating the body of 2-arg method, this method can just call the 2-arg constructor with `first=0`.  
  
```  
template <typename Integer>  
integer_range<Integer>  
irange(Integer last) {  
    return irange<Integer>(0, last)  
}


---

## Comment 2

> Username: nooploop  
> Created_at: 2017-11-15 20:10:22 UTC  
> Url: https://github.com/boostorg/range/pull/45#issuecomment-344713620  

I think this one was not ready for merge and is actually a duplicate of #43. Should be reverted.

---
