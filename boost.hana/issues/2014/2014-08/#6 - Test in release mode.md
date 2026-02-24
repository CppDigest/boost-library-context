# #6 - Test in release mode [Closed]

> Username: gnzlbg  
> Created at: 2014-08-07 08:59:41 UTC  
> Updated at: 2014-10-05 04:51:07 UTC  
> Closed at: 2014-10-05 04:51:07 UTC  
> Url: https://github.com/boostorg/hana/issues/6  

When compiling in Release mode (`DCMAKE_BUILD_TYPE=Release`) CMake passes the flag `-DNDEBUG` which disables `assert(...)`. The problem is that `assert` is used by e.g. `BOOST_HANA_RUNTIME_ASSERT` so these checks are not performed in Release mode. Maybe a different macro to perform runtime checks during the tests could be provided.

---

## Comment 1

> Username: ldionne  
> Created at: 2014-08-07 12:59:12 UTC  
> Url: https://github.com/boostorg/hana/issues/6#issuecomment-51467582  

These macros are not part of the public interface of the library (perhaps they should be?), and they are only meant to be used in the unit tests. I'm just trying to understand; is there any reason why you would want to compile the unit tests in Release mode? Are you using these macros for another purpose?

---

## Comment 2

> Username: gnzlbg  
> Created at: 2014-08-07 13:04:13 UTC  
> Updated at: 2014-08-07 13:05:21 UTC  
> Url: https://github.com/boostorg/hana/issues/6#issuecomment-51468063  

User code that uses the library will run in Release mode. Is there a reason why the library is only tested in debug mode? I need to trust that the library also passes its test when compiled in Release mode and right now everything passes but all the run-time tests are not performed.

---

## Comment 3

> Username: ldionne  
> Created at: 2014-08-07 13:08:24 UTC  
> Url: https://github.com/boostorg/hana/issues/6#issuecomment-51468548  

No, I guess it should be tested in both. Any idea of an alternative definition for `BOOST_HANA_RUNTIME_ASSERT`?

---

## Comment 4

> Username: gnzlbg  
> Created at: 2014-08-07 13:14:02 UTC  
> Updated at: 2014-08-07 13:15:27 UTC  
> Url: https://github.com/boostorg/hana/issues/6#issuecomment-51469191  

I think `BOOST_HANA_RUNTIME_ASSERT` should be kept as is and used in the library internals such that it is disabled in Release mode (as it is now).   
  
I think it would be better to just replace in the tests the macro `BOOST_HANA_RUNTIME_ASSERT` with another macro `BOOST_HANA_RUNTIME_TEST` that isn't disabled in release mode.   
  
I personally like the minimal [`CHECK`](https://github.com/ericniebler/range-v3/blob/master/test/simple_test.hpp) testing macro used by Eric Niebler in `range-v3` since it gets the job done without external dependencies and keeping include times down (it's just a small header).

---

## Comment 5

> Username: ldionne  
> Created at: 2014-10-05 04:51:07 UTC  
> Url: https://github.com/boostorg/hana/issues/6#issuecomment-57926684  

I fixed this issue in ae636b9. Now, the `BOOST_HANA_RUNTIME_ASSERT` macro will always assert, even in Release mode. I basically rolled my own little `assert` macro. If I find myself needing to perform runtime assertions inside the library (and not only in the unit tests), I will add another set of macros that are disabled in Release mode. Thanks for reporting, and sorry for leaving this open for so long.
