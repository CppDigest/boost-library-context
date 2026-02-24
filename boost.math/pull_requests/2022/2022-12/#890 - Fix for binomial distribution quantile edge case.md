# #890 Fix for binomial distribution quantile edge case [Merged]

> Username: mborland  
> Created at: 2022-12-03 17:50:36 UTC  
> Updated at: 2022-12-04 14:56:04 UTC  
> Merged at: 2022-12-04 14:56:01 UTC  
> Closed at: 2022-12-04 14:56:01 UTC  
> Url: https://github.com/boostorg/math/pull/890  

Closes #889

---

## Comment 1

> Username: mborland  
> Created_at: 2022-12-03 20:08:38 UTC  
> Url: https://github.com/boostorg/math/pull/890#issuecomment-1336249494  

@NAThompson can you double check this?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-12-04 09:30:44 UTC  
> Url: https://github.com/boostorg/math/pull/890#issuecomment-1336359447  

That looks fine to me, looks like we adopted a random convention because it wasn't clear what the correct thing to do was, I very much doubt anyone is relying on the old behaviour for such an edge case.

---
