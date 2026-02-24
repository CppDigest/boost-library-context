# #22 Add `to_underlying` [Merged]

> Username: k3DW  
> Created at: 2025-09-04 01:37:08 UTC  
> Updated at: 2025-09-05 16:27:35 UTC  
> Merged at: 2025-09-05 15:48:05 UTC  
> Closed at: 2025-09-05 15:48:05 UTC  
> Url: https://github.com/boostorg/compat/pull/22  



---

## Review 1 [Commented]

> Username: cmazakas  
> Created_at: 2025-09-04 01:46:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compat/pull/22#pullrequestreview-3183136000  

📁 test/to_underlying_test.cpp

```diff
  47 |+     {
  48 |+         auto output = boost::compat::to_underlying(unscoped_int8_t_value);
  49 |+         static_assert(std::is_same<decltype(output), std::int8_t>::value, "");
```

> Username: cmazakas  
> Created_at: 2025-09-04 01:46:42 UTC  
> Url: https://github.com/boostorg/compat/pull/22#discussion_r2320608226  

These static_asserts should be replaced by BOOST_TEST_TRAIT_TRUE  
https://www.boost.org/doc/libs/develop/libs/core/doc/html/core/lightweight_test.html#core.lightweight_test.header_boost_core_lightweight_t0  
  
The basic gist is that a failing static assert shouldn't be enough to halt the entire build.

> Username: pdimov  
> Created_at: 2025-09-04 01:49:04 UTC  
> Url: https://github.com/boostorg/compat/pull/22#discussion_r2320610574  

Or BOOST_TEST_TRAIT_SAME in this case.

> Username: k3DW  
> Created_at: 2025-09-04 02:01:02 UTC  
> Updated_at: 2025-09-04 02:01:03 UTC  
> Url: https://github.com/boostorg/compat/pull/22#discussion_r2320622853  

Thanks, I made the change


---

## Comment 2

> Username: pdimov  
> Created_at: 2025-09-04 09:02:55 UTC  
> Url: https://github.com/boostorg/compat/pull/22#issuecomment-3252606737  

I wonder whether we need the `static_assert` inside `to_underlying`. Since `std::underlying_type<E>` has no member `type` when `E` is not an enum, the function is SFINAE-d out in that case, so it can never be called.

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-09-04 09:04:06 UTC  
> Url: https://github.com/boostorg/compat/pull/22#issuecomment-3252611439  

https://godbolt.org/z/bh5qn9K86

---

## Comment 4

> Username: pdimov  
> Created_at: 2025-09-05 10:27:50 UTC  
> Url: https://github.com/boostorg/compat/pull/22#issuecomment-3257868835  

Well?

---

## Comment 5

> Username: k3DW  
> Created_at: 2025-09-05 14:33:11 UTC  
> Url: https://github.com/boostorg/compat/pull/22#issuecomment-3258575581  

Looks like the `static_assert` doesn't get hit on any of the big 3 compilers. I'll remove it

---
