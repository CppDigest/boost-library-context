# #55 Fix https://github.com/boostorg/lockfree/issues/35 [Merged]

> Username: austin-beer  
> Created at: 2019-12-02 18:27:59 UTC  
> Updated at: 2019-12-06 14:42:09 UTC  
> Merged at: 2019-12-06 14:40:49 UTC  
> Closed at: 2019-12-06 14:40:49 UTC  
> Url: https://github.com/boostorg/lockfree/pull/55  

* Change runtime asserts to compile-time asserts  
* Improve documentation for when capacity should be specified

---

## Comment 1

> Username: timblechmann  
> Created_at: 2019-12-03 02:35:36 UTC  
> Url: https://github.com/boostorg/lockfree/pull/55#issuecomment-560971629  

it basically reverts 505c1c04c18939cfd8fbf58386230ee78f4549ad ... iirc this patch would prevent the classes from being externally instantiated.  
  
afaict the proper way to go is to use `enable_if`

---

## Comment 2

> Username: timblechmann  
> Created_at: 2019-12-03 02:37:39 UTC  
> Url: https://github.com/boostorg/lockfree/pull/55#issuecomment-560972075  

could you separate out the doc strings from the assertions? i'm happy to merge that part

---

## Comment 3

> Username: austin-beer  
> Created_at: 2019-12-04 19:13:21 UTC  
> Url: https://github.com/boostorg/lockfree/pull/55#issuecomment-561795967  

Closing and re-opening PR to re-trigger CI builds

---

## Comment 4

> Username: austin-beer  
> Created_at: 2019-12-05 21:34:26 UTC  
> Url: https://github.com/boostorg/lockfree/pull/55#issuecomment-562328570  

The build failures are only with clang, and appear to be unrelated to changes in this PR, so I think it can still be merged.

---

## Comment 5

> Username: austin-beer  
> Created_at: 2019-12-06 14:08:02 UTC  
> Url: https://github.com/boostorg/lockfree/pull/55#issuecomment-562584847  

The previous commit fixed one of the two CI failures (it fixed clang on linux with libstdc++).

---

## Comment 6

> Username: timblechmann  
> Created_at: 2019-12-06 14:41:02 UTC  
> Url: https://github.com/boostorg/lockfree/pull/55#issuecomment-562596798  

thanks a lot, also for the CI failure fix!

---
