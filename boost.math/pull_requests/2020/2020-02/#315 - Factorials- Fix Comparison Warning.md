# #315 Factorials: Fix Comparison Warning [Merged]

> Username: ax3l  
> Created at: 2020-02-15 08:32:00 UTC  
> Updated at: 2020-02-15 22:02:39 UTC  
> Merged at: 2020-02-15 19:47:39 UTC  
> Closed at: 2020-02-15 19:47:39 UTC  
> Url: https://github.com/boostorg/math/pull/315  

Fix GCC warning:  
```  
warning #186-D: pointless comparison of unsigned integer with zero  
```  
  
`n` is an unsigned and always `>= 0`.

---

## Comment 1

> Username: ax3l  
> Created_at: 2020-02-15 08:39:18 UTC  
> Updated_at: 2020-02-15 08:39:28 UTC  
> Url: https://github.com/boostorg/math/pull/315#issuecomment-586568472  

cc @NAThompson: I just saw this when compiling on Summit.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2020-02-15 13:14:38 UTC  
> Url: https://github.com/boostorg/math/pull/315#issuecomment-586589273  

@ax3l : Thanks!  
  
How does the library work with the `xl` compiler? We've had zero success getting IBM into our CI.

---

## Comment 3

> Username: ax3l  
> Created_at: 2020-02-15 18:50:48 UTC  
> Url: https://github.com/boostorg/math/pull/315#issuecomment-586630281  

I don't know, this warning is unrelated to XL questions I had the other year.

---

## Comment 4

> Username: ax3l  
> Created_at: 2020-02-15 19:39:19 UTC  
> Url: https://github.com/boostorg/math/pull/315#issuecomment-586634296  

@NAThompson ready for merge :)

---

## Comment 5

> Username: NAThompson  
> Created_at: 2020-02-15 19:49:01 UTC  
> Url: https://github.com/boostorg/math/pull/315#issuecomment-586635105  

oh yeah, your PR did say "GCC warning" . . . I can't read.

---

## Comment 6

> Username: ax3l  
> Created_at: 2020-02-15 22:02:39 UTC  
> Url: https://github.com/boostorg/math/pull/315#issuecomment-586645420  

No prob, thanks for merging! :)

---
