# #564 Fix/line interpolate [Merged]

> Username: awulkiew  
> Created at: 2019-03-04 17:28:43 UTC  
> Updated at: 2019-03-05 08:03:56 UTC  
> Merged at: 2019-03-05 08:03:55 UTC  
> Closed at: 2019-03-05 08:03:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/564  

Fix compilation error with msvc-14.0.  
  
[test] Fix dereference of end iterator in case when range sizes are not equal.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2019-03-04 17:37:21 UTC  
> Updated_at: 2019-03-04 17:37:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/564#issuecomment-469344132  

I also get failing cases but I'd prefer to leave fixing it to @vissarion. Looks like some numerical issue:  
  
    MULTIPOINT((1.4 1),(1.8 1),(2 1.2),(2 1.6),(2 2),(1.6 2),(1.2 2),(1 2.2),(1 2.6),(1 3)) != MULTIPOINT((1.4 1),(1.8 1),(2 1.2),(2 1.6),(2 2),(1.6 2),(1.2 2),(1 2.2),(1 2.6))  
    MULTIPOINT((1.8 1),(2 1.6),(1.6 2),(1 2.2),(1 3)) != MULTIPOINT((1.8 1),(2 1.6),(1.6 2),(1 2.2))  
    MULTIPOINT((1.79997 1.00002),(2 1.59985),(1.60006 2.00007),(1 2.20015),(1 3)) != MULTIPOINT((1.79997 1.00002),(2 1.59985),(1.60006 2.00007),(1 2.20015))

---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2019-03-05 08:03:29 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/564#pullrequestreview-210536063  

Thanks!

---
