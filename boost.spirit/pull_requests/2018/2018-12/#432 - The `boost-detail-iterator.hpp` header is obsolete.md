# #432 The `boost/detail/iterator.hpp` header is obsolete [Merged]

> Username: Kojoley  
> Created at: 2018-12-29 15:49:41 UTC  
> Updated at: 2018-12-30 11:35:03 UTC  
> Merged at: 2018-12-30 11:35:00 UTC  
> Closed at: 2018-12-30 11:35:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/432  

Both `iterator_traits` and `distance` in `boost::detail` namespace are just  
aliases from `std` namespace.

---

## Review 1 [Commented]

> Username: hkaiser  
> Created_at: 2018-12-29 23:29:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/432#pullrequestreview-188424666  

📁 include/boost/spirit/home/classic/core/scanner/scanner.hpp

```diff
  14 | #include <boost/spirit/home/classic/core/match.hpp>
  15 | #include <boost/spirit/home/classic/core/non_terminal/parser_id.hpp>
  16 |- #include <boost/detail/iterator.hpp> // for boost::detail::iterator_traits
```

> Username: hkaiser  
> Created_at: 2018-12-29 23:29:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/432#discussion_r244518986  

Shouldn't we include `<iterator>` now?

> Username: Kojoley  
> Created_at: 2018-12-29 23:31:03 UTC  
> Updated_at: 2018-12-29 23:31:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/432#discussion_r244519076  

We are already, the first include in the file.


---
