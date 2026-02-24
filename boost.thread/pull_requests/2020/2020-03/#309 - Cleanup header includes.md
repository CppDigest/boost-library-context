# #309 Cleanup header includes [Merged]

> Username: Lastique  
> Created at: 2020-03-03 12:45:36 UTC  
> Updated at: 2026-02-06 11:04:44 UTC  
> Merged at: 2026-02-06 11:04:41 UTC  
> Closed at: 2026-02-06 11:04:41 UTC  
> Url: https://github.com/boostorg/thread/pull/309  

1. Make inclusion of `boost/bind/bind.hpp` conditional in some cases, when the code actually conditionally uses `boost::bind`. Reduces compile-time overhead and fixes https://github.com/boostorg/thread/issues/307.  
  
2. Remove some unnecessary uses of `boost::ref`. This allows to avoid including `boost/core/ref.hpp` in a few places, and avoids the associated template instantiation overhead in others.  
  
3. Replace deprecated header includes with the more recent alternatives. For example: `boost/detail/lightweight_test.hpp` -> `boost/core/lightweight_test.hpp`, `boost/ref.hpp` -> `boost/core/ref.hpp`.  
  
4. Replace some blanket includes with the more fine-grained ones. For example, `boost/utility.hpp`, `boost/atomic.hpp`. This reduces compile time overhead.  
  
5. Add some missing includes, for example, `boost/core/ref.hpp` and `boost/type_traits/is_same.hpp`.  
  
6. Replace uses of `std::is_same` with `boost::is_same` (with the corresponding included header) since the standard type_traits header presence and validity is not tested by the code. Using `boost::is_same` makes the code more portable.

---

## Comment 1

> Username: Lastique  
> Created_at: 2020-03-22 23:33:03 UTC  
> Url: https://github.com/boostorg/thread/pull/309#issuecomment-602296400  

@viboes @pdimov A kind reminder. Could we merge this to develop and master before 1.73?

---

## Comment 2

> Username: pdimov  
> Created_at: 2020-04-04 22:07:26 UTC  
> Url: https://github.com/boostorg/thread/pull/309#issuecomment-609095479  

This PR has too many unrelated changes so I didn't feel comfortable applying it. Instead, I just changed `<boost/bind.hpp>` to `<boost/bind/bind.hpp>` in c13beec81ce4b67f38359ebf6db8c2f8f02c3d62.

---

## Comment 3

> Username: Lastique  
> Created_at: 2020-04-04 22:31:52 UTC  
> Url: https://github.com/boostorg/thread/pull/309#issuecomment-609097771  

@pdimov Thanks. I will update this PR accordingly.

---
