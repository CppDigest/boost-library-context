# #397 Use endian macros from Predef [Merged]

> Username: Kojoley  
> Created at: 2018-09-15 15:20:29 UTC  
> Updated at: 2018-12-17 20:29:28 UTC  
> Merged at: 2018-09-26 11:13:41 UTC  
> Closed at: 2018-09-26 11:13:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/397  

The `boost/detail/endian.hpp` header is deprecated.

---

## Comment 1

> Username: djowel  
> Created_at: 2018-09-15 23:28:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/397#issuecomment-421653353  

It's about time. Thanks for doing this.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2018-09-26 11:13:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/397#issuecomment-424677440  

Appveyour fail is #400 and is not related to the PR.

---

## Comment 3

> Username: mwpowellhtx  
> Created_at: 2018-12-17 20:25:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/397#issuecomment-447986434  

The problem is not just for *X3*, I think. I am finding the same issue from *Spirit Qi* when I introduce a *Comment Skipper* (grammar unto itself) into the mix in lieu of the "standard" ``qi::space_type``. Suggestions?

---

## Comment 4

> Username: mwpowellhtx  
> Created_at: 2018-12-17 20:29:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/397#issuecomment-447987717  

Just noticed that last comment, and concerning [my report](/boostorg/spirit/issues/426)... I am in fact running against ``Boost 1.68.0``, so it seems I should migrate to ``1.69.0``.

---
