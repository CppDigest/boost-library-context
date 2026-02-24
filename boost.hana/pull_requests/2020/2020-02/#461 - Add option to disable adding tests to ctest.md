# #461 Add option to disable adding tests to ctest [Closed]

> Username: twam  
> Created at: 2020-02-07 16:22:50 UTC  
> Updated at: 2021-05-20 17:53:07 UTC  
> Closed at: 2021-05-20 17:53:07 UTC  
> Url: https://github.com/boostorg/hana/pull/461  

- Add cmake option BOOST_HANA_ENABLE_CTEST which configures  
  whether unit tests are to be added to cmake's ctest. Default  
  is ON to be backward-compatible.

---

## Comment 1

> Username: ldionne  
> Created_at: 2020-02-10 20:04:11 UTC  
> Url: https://github.com/boostorg/hana/pull/461#issuecomment-584325148  

What's the motivation for doing this?

---

## Comment 2

> Username: twam  
> Created_at: 2020-02-10 20:46:36 UTC  
> Url: https://github.com/boostorg/hana/pull/461#issuecomment-584345844  

Including the whole repo as a submodule into a cmake project and adding it with `add_subdirectory` without running the unit tests each time the project's unit test run. Other libraries provide similar options, e.g. https://github.com/google/googletest/blob/master/googletest/CMakeLists.txt#L18

---
