# #117 - `r & f` doesn't work when `r` is `result<void>` [Closed]

> Username: pdimov  
> Created at: 2024-01-30 23:28:17 UTC  
> Updated at: 2024-02-01 01:25:48 UTC  
> Closed at: 2024-02-01 01:25:48 UTC  
> Url: https://github.com/boostorg/system/issues/117  

Should accept a nullary `f`.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-01-30 23:30:14 UTC  
> Url: https://github.com/boostorg/system/issues/117#issuecomment-1918087909  

Actually, for `result<void>`, maybe `r & v` should also work.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-01-31 03:45:47 UTC  
> Url: https://github.com/boostorg/system/issues/117#issuecomment-1918329071  

`r & v` would be ambiguous with `r & f`, because it's unconstrained; any `v` works and produces `result<V>`. Won't be provided for now.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-01-31 09:47:52 UTC  
> Url: https://github.com/boostorg/system/issues/117#issuecomment-1918749112  

Now `r = r & f;` works for `result<void>`, but `r &= f;` does not. It's not a very useful operation, but should probably work for consistency reasons.
