# #9 Add missing headers in get.hpp and element_index.hpp [Merged]

> Username: awulkiew  
> Created at: 2014-12-31 15:51:04 UTC  
> Updated at: 2015-01-08 12:12:20 UTC  
> Merged at: 2015-01-08 10:58:13 UTC  
> Closed at: 2015-01-08 10:58:13 UTC  
> Url: https://github.com/boostorg/variant/pull/9  

If `boost/variant/get.hpp` is included before other Variant headers the compiler can't find `boost::recursive_wrapper<>`.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-01-07 10:08:25 UTC  
> Url: https://github.com/boostorg/variant/pull/9#issuecomment-69001736  

Actually, it should probably be fixed differently, i.e. `get` shouldn't depend on `recursive_wrapper`. `detail::variant_element_functor<>` should be partially specialized for `resursive_wrapper<>` in `resursive_wrapper_fwd.hpp`. Feel free to close this PR or let me know if I should prepare another one.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2015-01-08 11:09:14 UTC  
> Url: https://github.com/boostorg/variant/pull/9#issuecomment-69165744  

Commit https://github.com/boostorg/variant/commit/e00c75c20db74a89658fbfc927e9188068ef32a3 is also related to this ticket

---
