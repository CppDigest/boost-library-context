# #58 Remove unnecessary `allocator_traits` macro [Merged]

> Username: cmazakas  
> Created at: 2021-12-08 18:06:36 UTC  
> Updated at: 2021-12-28 16:18:33 UTC  
> Merged at: 2021-12-09 05:16:04 UTC  
> Closed at: 2021-12-09 05:16:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/58  



---

## Comment 1

> Username: glenfe  
> Created_at: 2021-12-08 21:34:56 UTC  
> Url: https://github.com/boostorg/unordered/pull/58#issuecomment-989214841  

You also have to remove:  
*  `BOOST_UNORDERED_HAS_FUNCTION` and  
*  `BOOST_UNORDERED_HAS_MEMBER`  and  
* `BOOST_UNORDERED_DEFAULT_TYPE_TMPLT`  
And thus remove:  
* `BOOST_UNORDERED_DEFAULT_EXPRESSION` and   
* `BOOST_UNORDERED_CHECK_EXPRESSION`  and   
* `BOOST_UNORDERED_CHECK_MEMBER` and   
* `BOOST_UNORDERED_DEFAULT_MEMBER`  
And thus remove  
* `detail::expr_test`  
* `detail::sfinae`  
i.e. All those macros and types are now not used anywhere, because they were only used in the `allocator_traits` implementation which has now been replaced with mine.

---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2021-12-08 21:38:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/58#pullrequestreview-826983730  

📁 test/Jamfile.v2

```diff
  32 |-         [ run unordered/compile_map.cpp : :
  33 |-             : <define>BOOST_UNORDERED_USE_ALLOCATOR_TRAITS=0
  32 |+         [ run unordered/compile_map.cpp : : :
```

> Username: pdimov  
> Created_at: 2021-12-08 21:38:20 UTC  
> Url: https://github.com/boostorg/unordered/pull/58#discussion_r765264834  

It's not necessary to run the same test twice. You can remove this one.


---
