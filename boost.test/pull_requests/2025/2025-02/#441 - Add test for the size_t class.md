# #441 Add test for the size_t class [Merged]

> Username: Flamefire  
> Created at: 2025-02-03 12:41:39 UTC  
> Updated at: 2025-02-03 19:56:13 UTC  
> Merged at: 2025-02-03 19:12:37 UTC  
> Closed at: 2025-02-03 19:12:37 UTC  
> Url: https://github.com/boostorg/test/pull/441  

Verify the constructor and increment/decrement operators  
  
I wasn't sure https://github.com/boostorg/test/blob/10185327515e968ab01c9a5a4017c603777e4cd7/include/boost/test/data/size.hpp#L51 works as expected as the templated ctor might be preferred which would not copy the `is_inf`  
  
The test also shows some inconsistencies in the over/underflow handling. It might be better to remove any operator taking a `std::size_t` which means those values will get converted to `data::size_t` and can be consistently handled.

---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2025-02-03 19:11:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/test/pull/441#pullrequestreview-2590786443  

LGTM

---

## Comment 2

> Username: mborland  
> Created_at: 2025-02-03 19:12:24 UTC  
> Url: https://github.com/boostorg/test/pull/441#issuecomment-2631847221  

> The test also shows some inconsistencies in the over/underflow handling. It might be better to remove any operator taking a `std::size_t` which means those values will get converted to `data::size_t` and can be consistently handled.  
  
Yeah, that would likely be an improvement.

---
