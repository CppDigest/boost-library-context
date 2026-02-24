# #119 Make operator / a friend of string_path [Merged]

> Username: ashtum  
> Created at: 2023-12-05 10:35:37 UTC  
> Updated at: 2023-12-05 16:50:08 UTC  
> Merged at: 2023-12-05 16:50:08 UTC  
> Closed at: 2023-12-05 16:50:08 UTC  
> Url: https://github.com/boostorg/property_tree/pull/119  



---

## Comment 1

> Username: ashtum  
> Created_at: 2023-12-05 14:56:22 UTC  
> Url: https://github.com/boostorg/property_tree/pull/119#issuecomment-1840959105  

@pdimov, Upon further consideration, I made the `operator/` overload a friend of `string_path` so that it can be implicitly constructed from string and char types when needed. With that, we don't need multiple overloads (the first commit in this PR). Is this the preferred approach?

---

## Comment 2

> Username: pdimov  
> Created_at: 2023-12-05 15:23:01 UTC  
> Url: https://github.com/boostorg/property_tree/pull/119#issuecomment-1841011600  

What are we trying to fix here? https://github.com/boostorg/property_tree/pull/24 and http://trac.cpp.al/trac10/ticket/12466?

---

## Comment 3

> Username: ashtum  
> Created_at: 2023-12-05 15:24:31 UTC  
> Url: https://github.com/boostorg/property_tree/pull/119#issuecomment-1841014799  

> What are we trying to fix here? #24 and http://trac.cpp.al/trac10/ticket/12466?  
  
Yes.

---

## Comment 4

> Username: pdimov  
> Created_at: 2023-12-05 15:26:43 UTC  
> Url: https://github.com/boostorg/property_tree/pull/119#issuecomment-1841019091  

This is the preferred approach, yes. But it looks like we are missing a test for `p1 / p2` with `p1` and `p2` both being paths.

---

## Review 5 [Commented]

> Username: ashtum  
> Created_at: 2023-12-05 15:43:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/property_tree/pull/119#pullrequestreview-1765436797  

📁 test/test_property_tree.hpp

```diff
1034 |+         path p = path(T("key1.key2")) / path(T("key3"));
1035 |+         BOOST_TEST(pt.get<int>(p, 0) == 1);
1036 |+     }
```

> Username: ashtum  
> Created_at: 2023-12-05 15:43:22 UTC  
> Updated_at: 2023-12-05 15:43:23 UTC  
> Url: https://github.com/boostorg/property_tree/pull/119#discussion_r1415837260  

> But it looks like we are missing a test for `p1 / p2` with `p1` and `p2` both being paths.  
  
@pdimov, Addressed.


---
