# #19 Add CMakeLists.txt to build mp11 as standalone library [Closed]

> Username: oliora  
> Created at: 2018-03-14 18:21:03 UTC  
> Updated at: 2018-10-03 06:53:25 UTC  
> Closed at: 2018-10-03 06:53:25 UTC  
> Url: https://github.com/boostorg/mp11/pull/19  

Note that mp11 still requires core Boost headers but can be build against  
older version of Boost

---

## Review 1 [Commented]

> Username: ax3l  
> Created_at: 2018-04-06 07:46:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mp11/pull/19#pullrequestreview-109972225  

📁 CMakeLists.txt

```diff
  15 |+ enable_testing()
  16 |+ 
  17 |+ find_package(Boost 1.56 REQUIRED)  # 1.56+ for lightweight_test
```

> Username: ax3l  
> Created_at: 2018-04-06 07:46:25 UTC  
> Updated_at: 2018-09-25 19:42:11 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#discussion_r179682371  

is this only required for tests?  
  
if so, you can react on the user-controllable var `BUILD_TESTING[=ON]` which is set by `enable_testing()` and only depend on Boost when testing is requested.  
  
This would allow truly stand-alone installs (without tests).

> Username: oliora  
> Created_at: 2018-05-11 17:51:42 UTC  
> Updated_at: 2018-09-25 19:42:11 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#discussion_r187686657  

Mp11 depends on `boost/config.hpp` header so I can't cut dependency on Boost.

> Username: pdimov  
> Created_at: 2018-08-29 23:15:54 UTC  
> Updated_at: 2018-09-25 19:42:11 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#discussion_r213863228  

I removed the dependency on Config by the way.

> Username: oliora  
> Created_at: 2018-09-24 09:45:57 UTC  
> Updated_at: 2018-09-25 19:42:11 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#discussion_r219779742  

I'll update the PR as @ax3l suggested

> Username: ax3l  
> Created_at: 2018-09-24 12:27:15 UTC  
> Updated_at: 2018-09-25 19:42:11 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#discussion_r219816759  

@oliora do you want to update the PR description accordingly? :)


---

## Comment 2

> Username: oliora  
> Created_at: 2018-07-02 17:32:58 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#issuecomment-401878364  

@pdimov any comment on my PR?

---

## Comment 3

> Username: pdimov  
> Created_at: 2018-07-02 21:36:30 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#issuecomment-401945217  

I no longer support standalone use, at least not officially, so I'm not sure about this. Supporting an older Boost requires f.ex. switching back from `config/workaround.hpp` to the deprecated `detail/workaround.hpp`. Same if I decide to make use of a new Boost.Config macro such as f.ex. `BOOST_NO_CXX17_FOLD_EXPRESSIONS` - it won't be present in an older Boost.

---

## Comment 4

> Username: oliora  
> Created_at: 2018-07-03 15:17:31 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#issuecomment-402193920  

@pdimov Makes sense but pity. It would be awesome to have mp11 as a standalone library especially with C++17 where most of vocabulary types like `variant` are in the STL so much more things can be done without depending on Boost (one is unfortunately a quite heavy dependency)

---

## Comment 5

> Username: oliora  
> Created_at: 2018-07-03 15:26:14 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#issuecomment-402196745  

Btw, is there any chance to get mp11 as part of the STL?

---

## Comment 6

> Username: pdimov  
> Created_at: 2018-07-03 16:07:11 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#issuecomment-402210058  

I have a proposal, http://open-std.org/JTC1/SC22/WG21/docs/papers/2018/p0949r0.html, but the committee isn't sure yet what metaprogramming facilities it wants. They'll need to decide one way or the other at the next meeting.

---

## Review 7 [Commented]

> Username: pdimov  
> Created_at: 2018-09-24 12:31:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mp11/pull/19#pullrequestreview-158080395  

📁 CMakeLists.txt

```diff
   1 |+ if(APPLE)
   2 |+     cmake_minimum_required(VERSION 3.5)
```

> Username: pdimov  
> Created_at: 2018-09-24 12:31:32 UTC  
> Updated_at: 2018-09-25 19:42:11 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#discussion_r219817849  

Out of curiosity, why is this different on Mac?

> Username: oliora  
> Created_at: 2018-09-24 16:22:00 UTC  
> Updated_at: 2018-09-25 19:42:11 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#discussion_r219899939  

That's a good question. I remember the reason was that before CMake 3.5 the generated Xcode projects were somehow screwed but I can't reproduce it now. I'll remove this condition since it might be somehow my machine specific issue.


---

## Review 8 [Commented]

> Username: oliora  
> Created_at: 2018-09-24 17:07:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mp11/pull/19#pullrequestreview-158203418  

📁 CMakeLists.txt

```diff
   1 |- cmake_minimum_required(VERSION 3.2)
   1 |+ cmake_minimum_required(VERSION 3.1)
```

> Username: oliora  
> Created_at: 2018-09-24 17:07:15 UTC  
> Updated_at: 2018-09-25 19:42:11 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#discussion_r219914837  

Since we don't use any CMake 3.2 features, we can relax the required version to 3.1. 3.1. is the version where `CMAKE_CXX_STANDARD` variable was introduced.  
  
P.S. Sorry for many small commits


---

## Review 9 [Commented]

> Username: oliora  
> Created_at: 2018-09-24 17:10:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mp11/pull/19#pullrequestreview-158204462  

📁 CMakeLists.txt

```diff
   1 |+ cmake_minimum_required(VERSION 3.1)
   2 |+ 
   3 |+ project(BoostMp11 VERSION 1.66.0 LANGUAGES CXX)
```

> Username: oliora  
> Created_at: 2018-09-24 17:10:10 UTC  
> Updated_at: 2018-09-25 19:42:11 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#discussion_r219915660  

The version should be probably 1.68 already. Time flies :)


---

## Review 10 [Commented]

> Username: oliora  
> Created_at: 2018-09-24 17:21:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mp11/pull/19#pullrequestreview-158208894  

📁 CMakeLists.txt

```diff
   4 |+ 
   5 |+ set(CMAKE_CXX_STANDARD 11)
   6 |+ set(CMAKE_CXX_STANDARD_REQUIRED ON)
```

> Username: oliora  
> Created_at: 2018-09-24 17:21:14 UTC  
> Updated_at: 2018-09-25 19:42:11 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#discussion_r219919245  

Since mp11 can optionally use features from C++17 and does its own C++ feature detection it's probably better to set `CMAKE_CXX_STANDARD_REQUIRED` to `OFF` and set `CMAKE_CXX_STANDARD` into the max standard known to CMake. I'd prefer to make it in a separate commit if possible.

> Username: ax3l  
> Created_at: 2018-09-25 08:58:02 UTC  
> Updated_at: 2018-09-25 19:42:11 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#discussion_r220110610  

If you set `CMAKE_CXX_STANDARD` to the max know to CMake you will exclude a lot of compilers, such as NVCC, that do not yet have e.g. C++17 support. Update: oh wait, yes might work with `CMAKE_CXX_STANDARD_REQUIRED` to `OFF`.

> Username: oliora  
> Created_at: 2018-09-25 19:39:53 UTC  
> Updated_at: 2018-09-25 19:42:11 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#discussion_r220328258  

Yep. But I've just realized that the C++ standard is really applied only to tests since the library itself is header-only. For tests it would be the best to test different language versions to make sure that mp11 compatibility layer works fine and library is compatible with all C++ versions (C++11, C++14 and C++17 for now). That's quite easy to add and I'll do it later in a separate PR.

> Username: ax3l  
> Created_at: 2018-09-25 21:10:40 UTC  
> Updated_at: 2018-09-25 21:10:58 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#discussion_r220356701  

Great point, in that case only set the requirement on the tests (per test target) instead of putting it on the library target :)

> Username: oliora  
> Created_at: 2018-09-27 09:10:57 UTC  
> Updated_at: 2018-09-27 09:10:58 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#discussion_r220848462  

Let's keep it like it is for this PR. I'd prefer to move c++11 requirement to test targets in a separate commit.


---

## Review 11 [Commented]

> Username: pdimov  
> Created_at: 2018-09-28 13:10:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mp11/pull/19#pullrequestreview-159852011  

📁 CMakeLists.txt

```diff
  19 |+ 
  20 |+ if (BUILD_TESTING)
  21 |+     find_package(Boost 1.56 REQUIRED)  # 1.56+ for lightweight_test
```

> Username: pdimov  
> Created_at: 2018-09-28 13:10:52 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#discussion_r221246835  

Travis of course has 1.55. FetchContent is best, but that's CMake 3.11, and Travis has 3.9. It ain't easy.

> Username: pdimov  
> Created_at: 2018-09-28 13:12:09 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#discussion_r221247187  

Ordinarily I'd just submodule Config/Core/Assert, but it's not appropriate for a Boost library to do that.


---

## Comment 12

> Username: pdimov  
> Created_at: 2018-09-30 22:30:13 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#issuecomment-425757314  

Thank you for this pull request. I've added a `CMakeLists.txt` that is roughly functionally identical to your proposed one, with certain minor differences that are expected to better fit the overall Boost CMake structure if/when that arrives.  
  
I've left the `export(EXPORT ...)` part commented out for now until I figure out if I need it and for what it might be useful. -)  
  
It's possible to `find_package(Boost 1.56)` first and then fetch as a fallback, but since Travis doesn't have Boost 1.56, there's no immediate need for that. :-)  
  
My next task here would probably be to support building the documentation with `cmake --build . --target doc`, and perhaps install it as well.  
  
If what I added strikes you as wrong, please let me know. I'm not a CMake expert by any means.

---

## Comment 13

> Username: oliora  
> Created_at: 2018-10-03 06:53:25 UTC  
> Url: https://github.com/boostorg/mp11/pull/19#issuecomment-426530591  

@pdimov the added CMake files look good for me. I’m closing my PR then.

---
