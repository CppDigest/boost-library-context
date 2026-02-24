# #84 Fix -Wconversion about float from unsigned int [Merged]

> Username: mloskot  
> Created at: 2018-04-15 20:18:27 UTC  
> Updated at: 2018-04-16 08:42:03 UTC  
> Merged at: 2018-04-16 04:18:28 UTC  
> Closed at: 2018-04-16 04:18:28 UTC  
> Url: https://github.com/boostorg/gil/pull/84  

### Description  
  
Use of `gil::float32_t` type members typedefs with variables for half-results.  
Observing intermediate values might be useful during debugging.  
  
-----  
  
It also aligns with the recent agreement to stick to 80-90 line length.  
The `return` statements in separate line might also be preferred or even blessed [in particular time, for breakpoints](https://www.youtube.com/watch?v=QTLn3goa3A8&t=10m20s).  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
### Environment  
  
All relevant information like:  
  
- Compiler version: GCC 7.3

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2018-04-15 22:09:02 UTC  
> Updated_at: 2018-04-15 22:10:20 UTC  
> Url: https://github.com/boostorg/gil/pull/84#issuecomment-381442000  

Now I understand my confusion: https://ci.appveyor.com/project/stefanseefeld/gil/build/1.0.351-develop contains failed builds, yet the overall status of the build is "pass". Strange...  
(scratch that, I now see the `allow_failures` entry...)  
  
Otherwise this looks good, but I'd prefer to wait for the Travis builds to finish, just for good form. :-)

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-04-15 22:16:26 UTC  
> Url: https://github.com/boostorg/gil/pull/84#issuecomment-381442422  

Sure, all CI must pass. Thank you.  
  
Regarding the confusion, [it is my fault](https://github.com/boostorg/gil/blob/develop/.appveyor.yml#L52-L67) - an attempt (or cheating) to avoid permanent CI failures :)

---

## Review 3 [Approved]

> Username: chhenning  
> Created_at: 2018-04-15 22:16:46 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/84#pullrequestreview-112253332  

Looks good.

---
