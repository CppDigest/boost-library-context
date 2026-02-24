# #94 - Add "monadic" operations to result [Closed]

> Username: pdimov  
> Created at: 2022-09-30 11:17:00 UTC  
> Updated at: 2025-12-01 10:12:06 UTC  
> Closed at: 2025-12-01 10:12:06 UTC  
> Url: https://github.com/boostorg/system/issues/94  

`r & f` -> `r? f(*r): r.error()`  
`r | x` -> `r? r: x`  
`r | f` -> `r? r: f()`  
  
https://godbolt.org/z/YWKKbq859

---

## Comment 1

> Username: pdimov  
> Created at: 2023-03-25 02:14:10 UTC  
> Url: https://github.com/boostorg/system/issues/94#issuecomment-1483682461  

More like `r | x` -> `r? *r: x`, and similarly `r | f` -> `r? *r: f()`.

---

## Comment 2

> Username: pdimov  
> Created at: 2023-03-25 02:18:32 UTC  
> Url: https://github.com/boostorg/system/issues/94#issuecomment-1483683637  

`r &= f;` -> `if( r ) r = f(*r);`  
`r |= x;` -> `if( !r ) r = x;`  
`r |= f;` -> `if( !r ) r = f();`

---

## Comment 3

> Username: pdimov  
> Created at: 2023-10-31 17:18:49 UTC  
> Url: https://github.com/boostorg/system/issues/94#issuecomment-1787650164  

Implemented on develop (without `|=` which is deemed unnecessary.)  
  
In the future, maybe consider error-transforming variants of `|`, as in  
  
`r | f` -> `r? *r: f( r.error() )`

---

## Comment 4

> Username: pdimov  
> Created at: 2024-01-31 23:23:48 UTC  
> Url: https://github.com/boostorg/system/issues/94#issuecomment-1920165630  

`r |= x` should probably be provided for consistency, always with the effective semantics of `r = r | x`.  
  
It only makes sense for `r |= f;` where `f` returns `result`, but it's probably less surprising to just support all the cases.

---

## Comment 5

> Username: pdimov  
> Created at: 2024-02-02 07:43:21 UTC  
> Url: https://github.com/boostorg/system/issues/94#issuecomment-1923239614  

`r |= x` implemented on develop.
