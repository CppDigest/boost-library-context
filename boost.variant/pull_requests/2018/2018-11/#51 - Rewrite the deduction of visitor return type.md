# #51 Rewrite the deduction of visitor return type [Merged]

> Username: ldionne  
> Created at: 2018-11-15 23:59:03 UTC  
> Updated at: 2021-09-20 18:35:07 UTC  
> Merged at: 2018-12-23 13:34:21 UTC  
> Closed at: 2018-12-23 13:34:21 UTC  
> Url: https://github.com/boostorg/variant/pull/51  

This avoids using `boost::declval` inside evaluated contexts, which is invalid and will actually be diagnosed [by compilers][1] when the type used inside `boost::declval` has internal linkage (such as an anonymous namespace).  
  
[1]: https://bugs.llvm.org/show_bug.cgi?id=35842

---

## Review 1 [Commented]

> Username: ldionne  
> Created_at: 2018-11-16 00:01:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/variant/pull/51#pullrequestreview-175611406  

📁 include/boost/variant/detail/apply_visitor_unary.hpp

```diff
 142 |-             return deduce_impl(next_t(), ++helper);
 143 |-         }
 128 |+         typedef decltype(true ? boost::declval< Visitor& >()( boost::declval< value_t& >() )
```

> Username: ldionne  
> Created_at: 2018-11-16 00:01:19 UTC  
> Url: https://github.com/boostorg/variant/pull/51#discussion_r234051976  

I interpreted the original code as trying to trigger return type deduction to make sure the visitor always returned __the same__ type for each type in the variant. Is this correct?  
  
If so, this patch will change how deduction is done to figure a common type between the return types of the visitor. It should be possible to change that if that is not desirable.


---

## Review 2 [Commented]

> Username: ldionne  
> Created_at: 2018-11-16 00:04:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/variant/pull/51#pullrequestreview-175611978  

📁 test/Jamfile.v2

```diff
  30 |     [ run variant_comparison_test.cpp ]
  31 |+     [ run variant_visit_internal_linkage.cpp : : :
  32 |+       <toolset>darwin:<cxxflags>-std=c++14
```

> Username: ldionne  
> Created_at: 2018-11-16 00:04:07 UTC  
> Url: https://github.com/boostorg/variant/pull/51#discussion_r234052428  

I'm terrible with Boost.Build. What I want to achieve here is to *always* run the test with `-std=c++14` if that is enabled, otherwise skip the test (because it will fail).

> Username: apolukhin  
> Created_at: 2018-11-16 09:29:58 UTC  
> Url: https://github.com/boostorg/variant/pull/51#discussion_r234139272  

It should be something close to `<cxxstd>14`, but I'm also terrible with Boost.Build. I'll double check in slack channel.

> Username: apolukhin  
> Created_at: 2018-11-16 10:07:42 UTC  
> Updated_at: 2018-11-16 10:07:43 UTC  
> Url: https://github.com/boostorg/variant/pull/51#discussion_r234150900  

```  
local below-c++14 = 03 11 ;  
[ run variant_visit_internal_linkage.cpp : : : <cxxstd>$(below-c++14)/<build>no ] ;  
```


---

## Review 3 [Changes requested]

> Username: apolukhin  
> Created_at: 2018-11-16 09:34:39 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/variant/pull/51#pullrequestreview-175718416  

📁 test/variant_visit_internal_linkage.cpp

```diff
   1 |+ //-----------------------------------------------------------------------------
```

> Username: apolukhin  
> Created_at: 2018-11-16 09:30:49 UTC  
> Updated_at: 2018-11-16 09:34:39 UTC  
> Url: https://github.com/boostorg/variant/pull/51#discussion_r234139500  

Add your name here and copyright year


---
