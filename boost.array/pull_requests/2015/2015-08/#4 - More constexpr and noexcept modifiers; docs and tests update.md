# #4 More constexpr and noexcept modifiers; docs and tests update [Closed]

> Username: apolukhin  
> Created at: 2015-08-05 20:10:10 UTC  
> Updated at: 2025-01-27 15:22:34 UTC  
> Closed at: 2025-01-27 15:22:34 UTC  
> Url: https://github.com/boostorg/array/pull/4  



---

## Comment 1

> Username: apolukhin  
> Created_at: 2017-01-22 09:34:26 UTC  
> Url: https://github.com/boostorg/array/pull/4#issuecomment-274319941  

This pull request makes boost::array closer to C++17 version of std::array. Any comments on it?

---

## Review 2 [Commented]

> Username: mclow  
> Created_at: 2017-01-23 15:08:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/array/pull/4#pullrequestreview-17942479  

📁 include/boost/array.hpp

```diff
 121 |-         /*BOOST_CONSTEXPR*/ const_reference operator[](size_type i) const 
 122 |-         {     
 123 |-             return BOOST_ASSERT_MSG( i < N, "out of range" ), elems[i];
```

> Username: mclow  
> Created_at: 2017-01-23 15:08:50 UTC  
> Url: https://github.com/boostorg/array/pull/4#discussion_r97336848  

If you write it using the comma operator, then it's only one statement and can (possibly) be constexpr in C++11. (Though Visual C++ will probably hark on it)


---

## Review 3 [Commented]

> Username: mclow  
> Created_at: 2017-01-23 15:10:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/array/pull/4#pullrequestreview-17942949  

📁 test/array_constexpr.cpp

```diff
  30 |- {
  31 |- //    constexpr int two = arr_std.at (2);
  32 |-     constexpr int three = arr.at (3);
```

> Username: mclow  
> Created_at: 2017-01-23 15:10:43 UTC  
> Url: https://github.com/boostorg/array/pull/4#discussion_r97337283  

at() should be constexpr, too - unless it throws.


---
