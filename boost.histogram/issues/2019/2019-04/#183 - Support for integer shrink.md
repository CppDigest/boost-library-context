# #183 - Support for integer shrink [Closed]

> Username: henryiii  
> Created at: 2019-04-22 22:13:05 UTC  
> Updated at: 2019-05-28 23:09:03 UTC  
> Closed at: 2019-05-28 23:09:03 UTC  
> Url: https://github.com/boostorg/histogram/issues/183  

I would like to have a version of the shrink option where I can set an upper or lower limit (or both) with a bin number instead of a coordinate. Ideally this could even work on all axis types, but not critical.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-04-26 08:30:45 UTC  
> Url: https://github.com/boostorg/histogram/issues/183#issuecomment-486974706  

It makes sense to support this in Python, because it already has slicing, but I am not sure how many people actually want to slice on indices.

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-05-05 18:46:35 UTC  
> Updated at: 2019-05-05 18:47:17 UTC  
> Url: https://github.com/boostorg/histogram/issues/183#issuecomment-489452589  

To implement this as part of the `reduce` function, I need a new "keyword". I already have `rebin`, `shrink` and `shrink_and_rebin`. What about `slice` and `slice_and_rebin`?

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-05-05 18:46:54 UTC  
> Url: https://github.com/boostorg/histogram/issues/183#issuecomment-489452615  

@jpivarski Any thoughts on names?

---

## Comment 4

> Username: jpivarski  
> Created at: 2019-05-05 19:00:06 UTC  
> Url: https://github.com/boostorg/histogram/issues/183#issuecomment-489453684  

It seems to me that `slice` is the appropriate name for this. `shrink` is unfamiliar to me, but I remember that conversation: it's an STL standard.

---

## Comment 5

> Username: HDembinski  
> Created at: 2019-05-28 23:09:03 UTC  
> Url: https://github.com/boostorg/histogram/issues/183#issuecomment-496722866  

implemented in develop
