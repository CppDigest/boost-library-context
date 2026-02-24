# #20 Add dependency of check-target on tests-target [Merged]

> Username: Flamefire  
> Created at: 2022-06-02 16:07:17 UTC  
> Updated at: 2022-06-03 07:19:59 UTC  
> Merged at: 2022-06-03 01:00:14 UTC  
> Closed at: 2022-06-03 01:00:14 UTC  
> Url: https://github.com/boostorg/cmake/pull/20  

This allows to build and run the tests by building (only) the `check` target  
  
The `check` target is created at https://github.com/boostorg/cmake/blob/bd981bfaff20ae4275ecbeba711c90e7fc9d0316/include/BoostRoot.cmake#L52  
  
CC @pdimov

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-06-02 16:46:03 UTC  
> Url: https://github.com/boostorg/cmake/pull/20#issuecomment-1145082673  

So Process has broken the CMake build, nice.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2022-06-02 20:29:42 UTC  
> Url: https://github.com/boostorg/cmake/pull/20#issuecomment-1145311485  

Yep but should be fixed now. Can you merge this? The changeset should be correct by inspection so no need to wait for another ci cycle

---
