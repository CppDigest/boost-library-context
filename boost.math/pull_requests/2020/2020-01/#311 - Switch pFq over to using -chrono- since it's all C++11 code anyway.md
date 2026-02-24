# #311 Switch pFq over to using <chrono> since it's all C++11 code anyway. [Merged]

> Username: jzmaddock  
> Created at: 2020-01-24 11:33:43 UTC  
> Updated at: 2020-02-06 12:57:11 UTC  
> Merged at: 2020-02-06 12:56:08 UTC  
> Closed at: 2020-02-06 12:56:08 UTC  
> Url: https://github.com/boostorg/math/pull/311  

Removes dependency on Boost.Chrono which breaks Microsoft's std lib build.  
Fixes https://github.com/boostorg/math/issues/310.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2020-01-24 19:13:41 UTC  
> Url: https://github.com/boostorg/math/pull/311#issuecomment-578262143  

@NAThompson : The CI tests are all failing for autodiff, looks like some disconnected change somewhere else has exposed a latent bug?  Should the use of `decay_t` refer to `std::decay_t` or `boost::decay_t` ?  I assume the former, but wanted to check.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2020-01-24 21:57:02 UTC  
> Url: https://github.com/boostorg/math/pull/311#issuecomment-578317710  

Didn't ever notice this before. It appears that `std::decay_t` would be fine.  
  
@pulver: Did you intend on `std::decay_t` or `boost::decay_t`?

---

## Comment 3

> Username: pulver  
> Created_at: 2020-01-24 22:14:28 UTC  
> Url: https://github.com/boostorg/math/pull/311#issuecomment-578323154  

> Did you intend on `std::decay_t` or `boost::decay_t`?  
  
I believe I would have intended `std::decay_t`. I am puzzled why it wasn't originally that way. I'll submit a fix this weekend, or feel free to make the change directly.

---
