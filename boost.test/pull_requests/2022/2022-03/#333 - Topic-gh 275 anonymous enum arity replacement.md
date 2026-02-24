# #333 Topic/gh 275 anonymous enum arity replacement [Merged]

> Username: raffienficiaud  
> Created at: 2022-03-05 13:42:14 UTC  
> Updated at: 2022-03-05 18:20:49 UTC  
> Merged at: 2022-03-05 18:20:46 UTC  
> Closed at: 2022-03-05 18:20:46 UTC  
> Url: https://github.com/boostorg/test/pull/333  

Build for #275

---

## Review 1 [Commented]

> Username: ecatmur  
> Created_at: 2022-03-05 16:38:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/test/pull/333#pullrequestreview-900990714  

📁 include/boost/test/data/monomorphic/initializer_list.hpp

```diff
  77 | 
  78 |-     enum { arity = 1 };
  78 |+     BOOST_STATIC_CONSTEXPR std::size_t arity = 1;
```

> Username: ecatmur  
> Created_at: 2022-03-05 16:38:37 UTC  
> Updated_at: 2022-03-05 16:38:38 UTC  
> Url: https://github.com/boostorg/test/pull/333#discussion_r820124016  

```diff  
-    BOOST_STATIC_CONSTEXPR std::size_t arity = 1;  
+    static const int arity = 1;  
```


---

## Comment 2

> Username: ecatmur  
> Created_at: 2022-03-05 16:53:34 UTC  
> Url: https://github.com/boostorg/test/pull/333#issuecomment-1059795795  

Yes, this looks good. I won't be able to test it in our code base till Monday, but I can't see any reason it wouldn't work.

---
