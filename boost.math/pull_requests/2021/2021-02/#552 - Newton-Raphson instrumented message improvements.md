# #552 Newton-Raphson instrumented message improvements [Merged]

> Username: NAThompson  
> Created at: 2021-02-23 14:32:11 UTC  
> Updated at: 2021-02-25 02:07:13 UTC  
> Merged at: 2021-02-25 02:07:09 UTC  
> Closed at: 2021-02-25 02:07:09 UTC  
> Url: https://github.com/boostorg/math/pull/552  

The Newton-Raphson method has an option to log convergence via `BOOST_MATH_INSTRUMENT`. However, too much information was give about some things which were not super helpful, and too little about residuals.  
  
In addition, remove buffer flushes as they slow down the process dramatically.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-02-23 15:21:58 UTC  
> Url: https://github.com/boostorg/math/pull/552#issuecomment-784278680  

@jzmaddock : I believe that all the boost files not in boost.math in roots.hpp are now unnecessary. Is this correct?

---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2021-02-23 15:49:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/552#pullrequestreview-596488969  

📁 include/boost/math/tools/roots.hpp

```diff
  14 | #include <utility>
  16 |- #include <boost/config/no_tr1/cmath.hpp>
  15 |+ #include <cmath.hpp>
```

> Username: jzmaddock  
> Created_at: 2021-02-23 15:49:13 UTC  
> Updated_at: 2021-02-24 15:40:01 UTC  
> Url: https://github.com/boostorg/math/pull/552#discussion_r581153312  

Should be `<cmath>` ?

> Username: NAThompson  
> Created_at: 2021-02-23 15:54:14 UTC  
> Updated_at: 2021-02-24 15:40:01 UTC  
> Url: https://github.com/boostorg/math/pull/552#discussion_r581157918  

omg. . .


---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-02-23 15:55:07 UTC  
> Url: https://github.com/boostorg/math/pull/552#issuecomment-784303553  

>  I believe that all the boost files not in boost.math in roots.hpp are now unnecessary. Is this correct?  
  
Looks like it, yes.  
  
iotream can go (unless BOOST_MATH_INSTRUMENT is set, but really this should be handled by boost/math/tools/config.hpp already.  
stdexcept can go (handled by policies/error_handling.hpp)  
  
I'm also not sure what #include <boost/math/tools/toms748_solve.hpp> is doing in there!

---

## Comment 4

> Username: NAThompson  
> Created_at: 2021-02-23 15:58:11 UTC  
> Url: https://github.com/boostorg/math/pull/552#issuecomment-784305798  

What about the tuples? They are part of C++11 now . ..

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2021-02-23 16:09:20 UTC  
> Url: https://github.com/boostorg/math/pull/552#issuecomment-784314151  

Yes, replacing `boost/math/tools/tuple.hpp` with `<tuple>` is on the list to do.  Note that the former is header just includes `<tuple>` anyway in C++11 and later.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2021-02-23 16:12:10 UTC  
> Url: https://github.com/boostorg/math/pull/552#issuecomment-784316312  

Actually... we probably don't need to include `<tuple>` at all, all we use is the concept of a tuple, not the actual type?

---

## Comment 7

> Username: NAThompson  
> Created_at: 2021-02-23 16:12:20 UTC  
> Url: https://github.com/boostorg/math/pull/552#issuecomment-784316430  

The boost::uint32_t might also be able to go; is there a way to remove it in a backwards compatible way?

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2021-02-23 16:38:49 UTC  
> Url: https://github.com/boostorg/math/pull/552#issuecomment-784335886  

I only see uintmax_t - that should be a straight replacement boost::uintmax_t => std::uintmax_t.   Hmmm, but those might be different (but same width) types?  And we should make that replacement library wide in one go probably.

---

## Comment 9

> Username: NAThompson  
> Created_at: 2021-02-24 13:26:24 UTC  
> Updated_at: 2021-02-24 13:28:48 UTC  
> Url: https://github.com/boostorg/math/pull/552#issuecomment-785073124  

> I'm also not sure what #include <boost/math/tools/toms748_solve.hpp> is doing in there!  
  
It defines a class `eps_tolerance`:  
  
```  
$ grep -nr 'eps_tolerance' ../include/boost/math/tools  
../include/boost/math/tools/toms748_solve.hpp:31:class eps_tolerance  
../include/boost/math/tools/toms748_solve.hpp:34:   eps_tolerance()  
../include/boost/math/tools/toms748_solve.hpp:38:   eps_tolerance(unsigned bits)  
```  
  
But it appears to only be used in the unit tests, not this file.

---

## Comment 10

> Username: NAThompson  
> Created_at: 2021-02-24 15:39:26 UTC  
> Updated_at: 2021-02-24 15:40:30 UTC  
> Url: https://github.com/boostorg/math/pull/552#issuecomment-785164591  

It looks like the [documentation](https://github.com/boostorg/math/blob/develop/doc/roots/roots_without_derivatives.qbk) lists it under the `root.hpp` header. So removing it is a backwards incompatible change.

---
