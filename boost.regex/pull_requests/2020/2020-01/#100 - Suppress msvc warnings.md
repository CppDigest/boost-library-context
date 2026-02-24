# #100 Suppress msvc warnings. [Merged]

> Username: jzmaddock  
> Created at: 2020-01-20 19:52:21 UTC  
> Updated at: 2022-03-19 16:30:04 UTC  
> Merged at: 2020-01-22 13:35:04 UTC  
> Closed at: 2020-01-22 13:35:04 UTC  
> Url: https://github.com/boostorg/regex/pull/100  

Fixes: https://github.com/boostorg/regex/issues/80

---

## Comment 1

> Username: pdimov  
> Created_at: 2020-01-20 22:46:39 UTC  
> Url: https://github.com/boostorg/regex/pull/100#issuecomment-576452218  

You need BOOST_NOEXCEPT_OR_NOTHROW for the exception destructors.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-01-21 10:31:42 UTC  
> Url: https://github.com/boostorg/regex/pull/100#issuecomment-576619162  

>You need BOOST_NOEXCEPT_OR_NOTHROW for the exception destructors.  
  
Nod.  Thanks!

---
