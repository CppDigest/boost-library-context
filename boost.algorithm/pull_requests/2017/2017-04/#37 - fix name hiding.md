# #37 fix name hiding [Closed]

> Username: DanielaE  
> Created at: 2017-04-22 16:41:37 UTC  
> Updated at: 2017-05-08 18:01:27 UTC  
> Closed at: 2017-04-24 12:43:06 UTC  
> Url: https://github.com/boostorg/algorithm/pull/37  

Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: DanielaE  
> Created_at: 2017-04-22 16:45:13 UTC  
> Url: https://github.com/boostorg/algorithm/pull/37#issuecomment-296385968  

I have these changes in my take of Boost.Algorithm for more than one year now without any noticable negative effects in real-world projects.

---

## Comment 2

> Username: mclow  
> Created_at: 2017-04-24 07:55:17 UTC  
> Url: https://github.com/boostorg/algorithm/pull/37#issuecomment-296564016  

I think I'd rather change the parameter names to `compute_bm_prefix` and `build_suffix_table` rather than the member variable names.

---

## Comment 3

> Username: DanielaE  
> Created_at: 2017-04-24 11:47:20 UTC  
> Url: https://github.com/boostorg/algorithm/pull/37#issuecomment-296633319  

Well, I took the chance of upgrading my vc14.0 compiler from update 3 to the very latest post-upgrade 3 version that comes with the vc14.0 build tools in VS2017. The issue seems to be gone now and I drop this change from my take on Boost.

---

## Comment 4

> Username: mclow  
> Created_at: 2017-04-24 12:43:06 UTC  
> Url: https://github.com/boostorg/algorithm/pull/37#issuecomment-296653470  

Ok; thanks for letting me know.  I will close this pull request.

---
