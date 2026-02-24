# #1265 Fix BOOST_GEOMETRY_CONSTEXPR call in index [Merged]

> Username: vissarion  
> Created at: 2024-03-27 12:23:27 UTC  
> Updated at: 2024-03-29 19:26:02 UTC  
> Merged at: 2024-03-28 11:03:12 UTC  
> Closed at: 2024-03-28 11:03:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1265  

This PR fixes the compilation error   
`error: macro "BOOST_GEOMETRY_CONSTEXPR" passed 2 arguments, but takes just 1`  
introduced by cb2f53d9d90f9d602833e87ebf9699890b4f74d7

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2024-03-27 15:16:00 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1265#pullrequestreview-1963662961  

lgtm

---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2024-03-28 21:19:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1265#pullrequestreview-1967378191  

📁 include/boost/geometry/index/detail/rtree/visitors/insert.hpp

```diff
 330 |         // This ensures that leafs bounds correspond to the stored elements
 331 |-         if BOOST_GEOMETRY_CONSTEXPR (std::is_same<Element, value_type>::value
 331 |+         if (BOOST_GEOMETRY_CONSTEXPR ((std::is_same<Element, value_type>::value
```

> Username: awulkiew  
> Created_at: 2024-03-28 21:19:42 UTC  
> Updated_at: 2024-03-28 21:20:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/1265#discussion_r1543737915  

Have you tested it with C++17?  
My guess is that this would not compile because it'd expand to  
```  
if (constepxr ((...)))  
```  
The problem here is the comma. I think you only have to put `std::is_same<Element, value_type>::value` in parentheses.

> Username: vissarion  
> Created_at: 2024-03-29 08:33:30 UTC  
> Updated_at: 2024-03-29 08:42:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1265#discussion_r1544237308  

You are right, the problem is the first parenthesis, either putting `std::is_same<Element, value_type>::value` or the whole expression in parantheses is working, but I will follow your suggestion because it is simpler and focuses on the problem which is the comma. I will open a new PR.

> Username: vissarion  
> Created_at: 2024-03-29 08:34:26 UTC  
> Updated_at: 2024-03-29 08:42:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1265#discussion_r1544237938  

Do we want to add testing with C++17 on CI?

> Username: awulkiew  
> Created_at: 2024-03-29 19:26:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1265#discussion_r1544804957  

Yes, I think it's a good idea. We have optional C++17-dependent code: inline variables, `std::any` adaptation, `std::variant` adaptation, `if constexpr`.


---
