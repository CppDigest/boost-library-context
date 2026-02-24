# #66 Add deprecation warnings for headers in detail dir [Merged]

> Username: Kojoley  
> Created at: 2019-11-23 13:16:33 UTC  
> Updated at: 2020-05-08 11:15:02 UTC  
> Merged at: 2020-05-08 10:56:34 UTC  
> Closed at: 2020-05-08 10:56:34 UTC  
> Url: https://github.com/boostorg/core/pull/66  



---

## Comment 1

> Username: Kojoley  
> Created_at: 2019-11-23 13:43:02 UTC  
> Url: https://github.com/boostorg/core/pull/66#issuecomment-557799002  

I grepped boost for using those headers:  
  
count | header  
---- | -----------------------  
1768 | detail/lightweight_test.hpp  
29   | detail/iterator.hpp  
23   | detail/no_exceptions_support.hpp  
8    | detail/scoped_enum.hpp  
  
and opt-outed of placing warning in `detail/lightweight_test.hpp` because of the obvious noise it will produce.

---

## Comment 2

> Username: pdimov  
> Created_at: 2019-12-29 03:44:56 UTC  
> Url: https://github.com/boostorg/core/pull/66#issuecomment-569471355  

Any objections to applying this?

---

## Comment 3

> Username: Lastique  
> Created_at: 2020-05-08 10:55:22 UTC  
> Url: https://github.com/boostorg/core/pull/66#issuecomment-625759733  

> Any objections to applying this?  
  
Not from me. Assuming we're ok, since noone objected so far.

---
