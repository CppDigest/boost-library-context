# #126 Don't construct stringstream multiple times [Merged]

> Username: zmij  
> Created at: 2019-03-26 15:03:20 UTC  
> Updated at: 2019-03-27 19:01:44 UTC  
> Merged at: 2019-03-27 19:01:36 UTC  
> Closed at: 2019-03-27 19:01:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/126  

It's inefficient to construct the stream object for each element in the array

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2019-03-26 22:00:24 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/126#issuecomment-476869853  

Good catch. Good idea. Thank you.

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2019-03-27 19:01:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/126#issuecomment-477305324  

Improve efficiency by not creating stringstream object multiple times in printing loop.

---
