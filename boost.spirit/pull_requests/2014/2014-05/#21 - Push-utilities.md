# #21 Push/utilities [Merged]

> Username: jamboree  
> Created at: 2014-05-02 14:08:09 UTC  
> Updated at: 2014-07-20 22:08:06 UTC  
> Merged at: 2014-05-06 00:41:35 UTC  
> Closed at: 2014-05-06 00:41:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/21  



---

## Comment 1

> Username: djowel  
> Created_at: 2014-05-04 23:54:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/21#issuecomment-42151269  

i'm confident the issues in the previous review have been met? anyway, let's wait a bit. seems agustin is travelling right now.

---

## Comment 2

> Username: djowel  
> Created_at: 2014-05-04 23:56:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/21#issuecomment-42151314  

BTW, shouldn't we use std::integer_sequence wherever available?

---

## Comment 3

> Username: K-ballo  
> Created_at: 2014-05-06 00:40:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/21#issuecomment-42257242  

All my important concerns have been addressed. We just have to remember whenever we switch from `mpl::bool_` to `std::integral_constant` that _is_callable_ is using `boost::integral_constant` instead.  
  
I don't think it's worth to support `std::integer_sequence` for now, as we'd need to write our own detection logic for that. We made some efforts to make this implementation of `integer_sequence` a drop in replacement for the `std::` one, so that we can get rid of it as soon as we upgrade our toolchains.

---
