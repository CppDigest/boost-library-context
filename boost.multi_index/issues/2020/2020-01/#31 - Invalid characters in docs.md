# #31 - Invalid characters in docs [Closed]

> Username: Lastique  
> Created at: 2020-01-09 10:30:09 UTC  
> Updated at: 2020-01-09 14:00:16 UTC  
> Closed at: 2020-01-09 14:00:15 UTC  
> Url: https://github.com/boostorg/multi_index/issues/31  

For example, on [this](https://www.boost.org/doc/libs/1_72_0/libs/multi_index/doc/reference/indices.html#complexity_signature) page, there are three question mark characters in places where an ellipsis (...) would be expected.

---

## Comment 1

> Username: joaquintides  
> Created at: 2020-01-09 10:57:25 UTC  
> Url: https://github.com/boostorg/multi_index/issues/31#issuecomment-572509020  

Ouch, thanks for reporting! I wonder how these have gone unnoticed by me for so long.  
  
Have you spotted weird chars anywhere else?

---

## Comment 2

> Username: Lastique  
> Created at: 2020-01-09 11:09:45 UTC  
> Updated at: 2020-01-09 11:11:28 UTC  
> Url: https://github.com/boostorg/multi_index/issues/31#issuecomment-572513519  

No, but I wasn't looking. Those are just the ones I spotted while skimming through that page. If there's an invalid character sequence in the docs source, it might be worth doing a search for it.

---

## Comment 3

> Username: joaquintides  
> Created at: 2020-01-09 14:00:15 UTC  
> Url: https://github.com/boostorg/multi_index/issues/31#issuecomment-572573744  

Fixed in c9a21d79d6aef4fcc90f53a55d569d0945803fb3.
