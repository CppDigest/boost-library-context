# #103 Add CMake install support (required for dependencies to have one) [Closed]

> Username: pdimov  
> Created at: 2019-12-28 15:26:58 UTC  
> Updated at: 2020-01-11 20:45:46 UTC  
> Closed at: 2020-01-11 20:45:45 UTC  
> Url: https://github.com/boostorg/predef/pull/103  

In CMake, in order to `install(EXPORT` a target, all dependent targets must have such support, otherwise it's a hard error at generation time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2019-12-29 02:33:45 UTC  
> Url: https://github.com/boostorg/predef/pull/103#issuecomment-569468142  

Interesting.. but how would this change work in a standalone version of the library? Would it make it possible to "install" it? What does installing do?

---

## Comment 2

> Username: pdimov  
> Created_at: 2019-12-29 03:13:34 UTC  
> Url: https://github.com/boostorg/predef/pull/103#issuecomment-569469885  

It's in principle possible to install Predef, because it has no dependencies, but it's more complicated. One option is to write the installation logic by hand, as I used to do with Mp11. Another is to use boost_install, but this requires (a) for you to acquire BoostInstall in some way, and (b) to have a version.  
  
In Mp11, I fetch tools/cmake with FetchContent, then use BoostInstall from there: https://github.com/boostorg/mp11/blob/develop/CMakeLists.txt  
  
I also maintain a version in sync with Boost.  
  
If you want to maintain a separate version, that's going to create ambiguities between the standalone Predef and the Boost one.  
  
Installing does the same as with b2; copies the headers into the specified include directory (default $CMAKE_INSTALL_PREFIX/include) and creates a CMake configuration file in the specified "cmake" directory (default $CMAKE_INSTALL_PREFIX/lib/cmake).

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-12-29 03:17:42 UTC  
> Url: https://github.com/boostorg/predef/pull/103#issuecomment-569470070  

In Mp11, the version is kept in sync with the superproject by having a test that checks ${PROJECT_VERSION} against BOOST_VERSION from config.hpp:  
  
https://github.com/boostorg/mp11/blob/develop/test/CMakeLists.txt#L10  
https://github.com/boostorg/mp11/blob/develop/test/check_cmake_version.cpp

---

## Comment 4

> Username: pdimov  
> Created_at: 2019-12-29 03:27:38 UTC  
> Url: https://github.com/boostorg/predef/pull/103#issuecomment-569470550  

By the way, if you omit LANGUAGES, the default is C CXX, which configures the C compiler whenever Predef is referenced. But that's probably correct, as you actually have C tests.  
  
It might be possible to only `enable_language(C)` when actually building the tests though. (And maybe enable `OBJC` and `OBJCXX` there as well.)

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2019-12-29 12:41:46 UTC  
> Url: https://github.com/boostorg/predef/pull/103#issuecomment-569501765  

1. IIRC `OBJC` and `OBJCXX` didn't exist when I first created this Cmakelist. Since when have those been language options?  
2. I don't plan to ever enable testing with cmake.  
3. I would really rather find a way to use vanilla cmake to enable install. Rather than relying on more Boost scripts. Is there something special that those scripts provide that can't be done with the plain cmake `install` function?

---

## Comment 6

> Username: pdimov  
> Created_at: 2019-12-29 12:55:01 UTC  
> Url: https://github.com/boostorg/predef/pull/103#issuecomment-569502869  

When inside the superproject, you need to at least include BoostInstall for the install variables that tell you where to install things, and if you already include it, it'd be a bit foolish to not just call it. As I said, I had a manual installation logic in Mp11 and threw it away, it makes no sense to duplicate BoostInstall and then track all its changes.  
  
If you insist on manually installing when outside the superproject, that's possible, but you need to decide how you'll handle versioning and whether you'll use the same package name ("boost_predef") as the Boost-distributed and installed Predef.  
  
In either case, this `else()` branch can be added later and does not affect the current patch.

---

## Comment 7

> Username: pdimov  
> Created_at: 2019-12-29 12:59:23 UTC  
> Url: https://github.com/boostorg/predef/pull/103#issuecomment-569503196  

>I don't plan to ever enable testing with cmake.  
  
It's convenient to have CMake tests if for no other reason but to check whether the CMake version matches the version in the header. Otherwise the two easily get out of sync. You could of course parse CMakeLists.txt from b2 or C++ or Python to achieve that if you're dead set on not testing with CMake.

---

## Comment 8

> Username: grafikrobot  
> Created_at: 2019-12-29 13:12:00 UTC  
> Updated_at: 2019-12-29 13:12:18 UTC  
> Url: https://github.com/boostorg/predef/pull/103#issuecomment-569504270  

> It's convenient to have CMake tests if for no other reason but to check whether the CMake version matches the version in the header. Otherwise the two easily get out of sync.  
  
I don't understand what that means? Which version? The cmake 3.0 version? Something else?

---

## Comment 9

> Username: grafikrobot  
> Created_at: 2019-12-29 13:19:10 UTC  
> Url: https://github.com/boostorg/predef/pull/103#issuecomment-569504872  

> When inside the superproject, you need to at least include BoostInstall for the install variables that tell you where to install things, and if you already include it, it'd be a bit foolish to not just call it. As I said, I had a manual installation logic in Mp11 and threw it away, it makes no sense to duplicate BoostInstall and then track all its changes.  
  
My reason is that I do most of the Boost Predef development outside of Boost in the standalone fork of the library "Hash Predef" (https://github.com/grafikrobot/hash-predef). And use a script to transform it to the Boost version. Hence including more Boost is a burden instead of a benefit.  
  
> If you insist on manually installing when outside the superproject, that's possible, but you need to decide how you'll handle versioning and whether you'll use the same package name ("boost_predef") as the Boost-distributed and installed Predef.  
>   
> In either case, this `else()` branch can be added later and does not affect the current patch.  
  
I guess one option is to move all the Boost specific cmake logic to a separate included file that only exists in the Boost version. But then I'd still be duplicating the install feature anyway. Since I would perhaps want to support letting users install with cmake (even if I personally dislike using build systems for install tasks).

---

## Comment 10

> Username: pdimov  
> Created_at: 2019-12-29 13:26:01 UTC  
> Url: https://github.com/boostorg/predef/pull/103#issuecomment-569505452  

>I don't understand what that means? Which version? The cmake 3.0 version? Something else?  
  
The library version. From CMake, users do `find_package(hash_predef 1.10)`, and want to get 1.10 or later. For this to work, you have to maintain a version in your CMakeLists.txt and then on installation create a -config-version.cmake file that embeds it. Since this version is present in both CMakeLists.txt and version.h, the two must match.  
  
>And use a script to transform it to the Boost version.  
  
I'll take a look at the source repo then.

---

## Comment 11

> Username: pdimov  
> Created_at: 2020-01-11 20:45:45 UTC  
> Url: https://github.com/boostorg/predef/pull/103#issuecomment-573352974  

I withdraw this PR for now, will look at some alternatives first.

---
