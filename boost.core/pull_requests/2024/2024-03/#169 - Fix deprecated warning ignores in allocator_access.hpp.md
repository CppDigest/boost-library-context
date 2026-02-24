# #169 Fix deprecated warning ignores in allocator_access.hpp [Merged]

> Username: k3DW  
> Created at: 2024-03-16 22:22:08 UTC  
> Updated at: 2024-06-19 01:42:35 UTC  
> Merged at: 2024-03-19 15:10:04 UTC  
> Closed at: 2024-03-19 15:10:04 UTC  
> Url: https://github.com/boostorg/core/pull/169  

Closes #168

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2024-03-16 23:12:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/169#pullrequestreview-1941344611  

📁 include/boost/core/allocator_access.hpp

```diff
  52 |+ # pragma warning(push)
  53 |+ # pragma warning(disable: 4996)
  54 |+ #elif defined(__GNUC__) && !(defined(__INTEL_COMPILER) || defined(__ICL) || defined(__ICC) || defined(__ECC)) && (__GNUC__ * 100 + __GNUC_MINOR__) >= 406
```

> Username: Lastique  
> Created_at: 2024-03-16 23:09:28 UTC  
> Updated_at: 2024-03-16 23:12:58 UTC  
> Url: https://github.com/boostorg/core/pull/169#discussion_r1527331034  

We have [`BOOST_GCC`](https://www.boost.org/doc/libs/1_84_0/libs/config/doc/html/boost_config/boost_macro_reference.html#boost_config.boost_macro_reference.boost_informational_macros) macro for this.


---

## Comment 2

> Username: pdimov  
> Created_at: 2024-03-17 00:38:26 UTC  
> Url: https://github.com/boostorg/core/pull/169#issuecomment-2002208489  

I don't think the GCC part is needed; we don't get any warnings from GCC.

---

## Comment 3

> Username: k3DW  
> Created_at: 2024-03-17 03:26:21 UTC  
> Url: https://github.com/boostorg/core/pull/169#issuecomment-2002292743  

I'll remove the GCC portion

---

## Comment 4

> Username: k3DW  
> Created_at: 2024-03-17 04:29:28 UTC  
> Url: https://github.com/boostorg/core/pull/169#issuecomment-2002303569  

Some of the drone GCC stages are showing failures with `deprecated-declarations`, so I'll add back the GCC portion, this time using `BOOST_GCC`

---

## Review 5 [Commented]

> Username: Lastique  
> Created_at: 2024-03-17 17:02:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/169#pullrequestreview-1941532626  

📁 test/Jamfile.v2

```diff
 266 | run allocator_destroy_n_test.cpp ;
 267 | run allocator_traits_test.cpp ;
 268 |+ run allocator_pmr_test.cpp ;
```

> Username: Lastique  
> Created_at: 2024-03-17 17:02:41 UTC  
> Url: https://github.com/boostorg/core/pull/169#discussion_r1527563096  

The test doesn't seem to execute any checks. Perhaps, it should be `compile` instead of `run`?

> Username: pdimov  
> Created_at: 2024-03-17 17:17:02 UTC  
> Url: https://github.com/boostorg/core/pull/169#discussion_r1527565523  

Or maybe `link`.

> Username: Lastique  
> Created_at: 2024-03-17 18:05:44 UTC  
> Url: https://github.com/boostorg/core/pull/169#discussion_r1527574686  

And while at it, if it doesn't need to execute any checks then it doesn't need to depend on `lightweight_test.hpp`.

> Username: k3DW  
> Created_at: 2024-03-18 01:10:39 UTC  
> Updated_at: 2024-03-18 01:13:06 UTC  
> Url: https://github.com/boostorg/core/pull/169#discussion_r1527667822  

With `link` it looks like I still need a `main()` function, but I can leave it out with `compile`. I'll change it to `compile`


---
