# #40 Feature/hash_is_avalanching [Merged]

> Username: joaquintides  
> Created at: 2025-05-26 17:12:30 UTC  
> Updated at: 2025-05-28 09:05:10 UTC  
> Merged at: 2025-05-28 09:05:10 UTC  
> Closed at: 2025-05-28 09:05:10 UTC  
> Url: https://github.com/boostorg/container_hash/pull/40  

This is part 1 of the process of migrating `hash_is_avalanching` from Boost.Unordered to Boost.ContainerHash; part 2 will redefine `boost::unordered::hash_is_avalanching` as a using declaration of `boost::container_hash::hash_is_avalanching` and deprecate `<boost/unordered/hash_traits.hpp>`.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2025-05-26 23:46:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/container_hash/pull/40#pullrequestreview-2869248390  

📁 test/hash_is_avalanching_test.cpp

```diff
  59 | #endif
  60 | 
  61 |+     BOOST_TEST_TRAIT_FALSE(( hash_is_avalanching< std::hash<std::string > > ));
```

> Username: pdimov  
> Created_at: 2025-05-26 23:46:23 UTC  
> Url: https://github.com/boostorg/container_hash/pull/40#discussion_r2107925072  

Why are you testing this?

> Username: joaquintides  
> Created_at: 2025-05-27 06:49:38 UTC  
> Url: https://github.com/boostorg/container_hash/pull/40#discussion_r2108329137  

It's an example of a struct without a nested `is_avalanching`.

> Username: pdimov  
> Created_at: 2025-05-27 08:04:40 UTC  
> Updated_at: 2025-05-27 08:04:41 UTC  
> Url: https://github.com/boostorg/container_hash/pull/40#discussion_r2108509351  

Please use a struct without a nested `is_avalanching`, then.  
  
I don't want to guarantee that `hash_is_avalanching` is false for `std::hash<std::string>`.


---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2025-05-26 23:48:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/container_hash/pull/40#pullrequestreview-2869249437  

📁 test/hash_is_avalanching_test.cpp

```diff
  62 |+     BOOST_TEST_TRAIT_TRUE(( hash_is_avalanching< X > ));
  63 |+     BOOST_TEST_TRAIT_TRUE(( hash_is_avalanching< Y > ));
  64 |+     BOOST_TEST_TRAIT_FALSE(( hash_is_avalanching< Z > ));
```

> Username: pdimov  
> Created_at: 2025-05-26 23:48:19 UTC  
> Url: https://github.com/boostorg/container_hash/pull/40#discussion_r2107925750  

Let's put these three in their own test (plus one without is_avalanching) and leave this one alone.


---

## Comment 3

> Username: pdimov  
> Created_at: 2025-05-27 09:17:54 UTC  
> Url: https://github.com/boostorg/container_hash/pull/40#issuecomment-2911798738  

Looks almost good to go. You just need to remove one include to make the cmake tests pass.

---

## Comment 4

> Username: pdimov  
> Created_at: 2025-05-27 13:58:41 UTC  
> Url: https://github.com/boostorg/container_hash/pull/40#issuecomment-2912623599  

I wonder whether `boost::container_hash` is the right namespace for this trait. It contains traits classes that are specific for ContainerHash and for its internal use, whereas this one is rather more general and public.  
  
`boost::` might be a better place for it. Its name is sufficiently prefixed with `hash_`, like everything else ContainerHash puts into `boost::`.

---
