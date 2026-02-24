# #12 - boost_test(TYPE run ...) seems to be broken on newer cmake versions [Closed]

> Username: HDembinski  
> Created at: 2021-06-27 13:24:29 UTC  
> Updated at: 2021-10-13 15:40:42 UTC  
> Closed at: 2021-10-13 15:40:42 UTC  
> Url: https://github.com/boostorg/cmake/issues/12  

I cannot locally or in CI use boost_test(TYPE run ...) anymore. cmake claims it cannot find the executable, see e.g.:  
  
https://github.com/boostorg/histogram/pull/328/checks?check_run_id=2920859956#step:4:49  
  
I am locally using cmake 3.18.4 and can reproduce this. I am not sure what version of cmake the CI is using. It seems that - since the test itself does not depend on the executable - , the executable is not build, although this used to work.  
  
As a workaround, I can use `make tests && make check`, which first builds all the tests and then runs them, but this is not as elegant as before.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-06-27 13:54:23 UTC  
> Url: https://github.com/boostorg/cmake/issues/12#issuecomment-869167608  

`make tests; ctest` is the new approach. It's indeed not as elegant as before, but is how CMake likes to do things (separate build and run phases), and allows parallelism. E.g. you can do `make -j32 tests; ctest -j32`, whereas you couldn't do this before (reliably).

---

## Comment 2

> Username: HDembinski  
> Created at: 2021-10-13 15:40:42 UTC  
> Url: https://github.com/boostorg/cmake/issues/12#issuecomment-942432130  

Ok, I think we can close this then.
