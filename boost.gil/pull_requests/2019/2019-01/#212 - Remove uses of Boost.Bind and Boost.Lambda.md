# #212 Remove uses of Boost.Bind and Boost.Lambda [Merged]

> Username: mloskot  
> Created at: 2019-01-13 00:06:00 UTC  
> Updated at: 2019-01-13 01:03:40 UTC  
> Merged at: 2019-01-13 01:03:25 UTC  
> Closed at: 2019-01-13 01:03:25 UTC  
> Url: https://github.com/boostorg/gil/pull/212  

Replace with `std::bind` and C++11 lambda functions.  
The two Boost libraries should no longer be a direct dependency of Boost.GIL.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Review 1 [Approved]

> Username: stefanseefeld  
> Created_at: 2019-01-13 00:08:58 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/212#pullrequestreview-191956033  

Awesome !

---
