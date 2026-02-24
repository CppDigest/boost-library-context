# #537 avoid copies, when comparing maps [Open]

> Username: domin144  
> Created at: 2025-12-13 19:55:43 UTC  
> Updated at: 2026-01-05 12:33:21 UTC  
> Url: https://github.com/boostorg/hana/pull/537  

There were two points, where copies were made:  
  
1. When binding a map for use in `partial`.  
  
This could be avoided, by using `std::ref`, but then either `partial` would have to handle `std::ref` in special way, by unwrapping the reference at the call point or the wrapped function would have to specify the accepted argument type explicitly to force cast.  
  
Instead, partial was replaced with a `compare_at_key` helper struct capturing the `maps` by reference.  
  
2. When returning the value from `find` wrapped into optional.  
  
This is avoided by using `at_key` instead of `find`.

---

## Comment 1

> Username: domin144  
> Created_at: 2025-12-13 20:00:40 UTC  
> Url: https://github.com/boostorg/hana/pull/537#issuecomment-3649759026  

The alternative approaches can be checked at:  
https://github.com/domin144/hana/tree/map_compare_no_copy_std_ref  
https://github.com/domin144/hana/tree/map_compare_no_copy_modified_partial

---

## Review 2 [Commented]

> Username: ricejasonf  
> Created_at: 2025-12-13 20:49:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hana/pull/537#pullrequestreview-3574557572  

📁 test/map/equal.cpp

```diff
 102 |+     auto map_unique_1 = hana::make_map(hana::make_pair(hana::integral_constant<int, 0>{}, std::make_unique<int>(0)));
 103 |+     auto map_unique_2 = hana::make_map(hana::make_pair(hana::integral_constant<int, 0>{}, std::make_unique<int>(0)));
 104 |+     BOOST_HANA_RUNTIME_CHECK(map_unique_1 != map_unique_2);
```

> Username: ricejasonf  
> Created_at: 2025-12-13 20:49:54 UTC  
> Url: https://github.com/boostorg/hana/pull/537#discussion_r2616522575  

Maybe add `map_unique_1 == map_unique_1`. I am not seeing that `hana::equal` tries to short circuit identical types (or even address).

> Username: domin144  
> Created_at: 2025-12-13 22:23:05 UTC  
> Updated_at: 2025-12-13 22:23:06 UTC  
> Url: https://github.com/boostorg/hana/pull/537#discussion_r2616564303  

good point.


---

## Comment 3

> Username: domin144  
> Created_at: 2026-01-05 12:33:21 UTC  
> Url: https://github.com/boostorg/hana/pull/537#issuecomment-3710243418  

UPDATE: I replaced `std::unique_ptr` with `ConstexprMoveOnly` to be more explicit in what is to be tested.

---
