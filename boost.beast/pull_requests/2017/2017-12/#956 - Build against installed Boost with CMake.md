# #956 Build against installed Boost with CMake [Closed]

> Username: TBBle  
> Created at: 2017-12-30 15:38:24 UTC  
> Updated at: 2018-06-26 14:44:58 UTC  
> Closed at: 2018-06-26 14:44:58 UTC  
> Url: https://github.com/boostorg/beast/pull/956  

These patches allow the CMake build script to build against a compiled version of Boost 1.66.0 or later.  
  
This requires passing in an extra flag to CMake, so should not affect existing users in any way, but makes it convenient for working on beast.  
  
Tested against the Boost.org 1.66.0 Windows installers for Visual Studio 2017 and Visual Studio 2015, and the Boost 1.66.0 version packaged for mingw-w64 in [msys2](http://www.msys2.org/) with gcc 7.2.0.  
  
I see no reason it wouldn't also work on Linux, but there's no Linux packages for Boost 1.66.0 yet, as far as I can tell.  
  
Note that CMake doesn't yet support Boost 1.66.0 from the upstream packages on Windows, pending [CMake Issue 17575](https://gitlab.kitware.com/cmake/cmake/issues/17575). An updated `FindBoost.cmake` is available in [CMake Merge Request 1625](https://gitlab.kitware.com/cmake/cmake/merge_requests/1625).

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2017-12-30 15:51:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/956#pullrequestreview-86032337  

📁 CMakeLists.txt

```diff
  29 |         /W4           # enable all warnings
  30 |         /MP           # Multi-processor compilation
  31 |+         /source-charset:ISO-8859-1 # base64.hpp is in ISO-8859-1 encoding
```

> Username: vinniefalco  
> Created_at: 2017-12-30 15:51:53 UTC  
> Updated_at: 2018-01-08 03:12:35 UTC  
> Url: https://github.com/boostorg/beast/pull/956#discussion_r159124438  

We should just fix base64.hpp to not have high-ASCII characters

> Username: TBBle  
> Created_at: 2017-12-30 15:56:20 UTC  
> Updated_at: 2018-01-08 03:12:35 UTC  
> Url: https://github.com/boostorg/beast/pull/956#discussion_r159124505  

Probably true. In this case, the non-ASCII character is (twice) in the copyright notice for the source pulled from http://www.adp-gmbh.ch/cpp/common/base64.html, and is that way in the original too, down to the encoding. It only complains if your local code page is non-European (cp932 in my case).  
  
Technically, changing that character would fail clause 3 of the license... It's also the author's name. Transcoding to UTF-8 is probably less problematic, if that gains us anything.


---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-12-30 20:47:50 UTC  
> Updated_at: 2017-12-30 20:48:39 UTC  
> Url: https://github.com/boostorg/beast/pull/956#issuecomment-354567486  

The problem is that this is not part of my workflow or the CI workflow (therefore, won't be tested regularly), and CMake support for Boost is not the greatest. FindBoost.cmake is always out of date. Beast does not really support CMake builds except for the one case of generating a Visual Studio project file for in-tree builds, this is explained in the README.md:  
https://github.com/boostorg/beast#building  
  
When there is better support for Boost in CMake I may revisit this but for now building the tests and examples is only supported in-tree using bjam.

---

## Comment 3

> Username: TBBle  
> Created_at: 2017-12-30 22:41:12 UTC  
> Updated_at: 2018-01-08 03:27:37 UTC  
> Url: https://github.com/boostorg/beast/pull/956#issuecomment-354572634  

Fair 'nuff. Since I've been working on improving Boost support in CMake recently, and have context; what sort of things do you feel need improving there? It's already pretty future-proof (you can just tell it to accept a given version even if it doesn't recognise it, and it'll assume the dependencies are the same as the most-recent version it knows), although the upstream Boost binaries for Windows changed filenames in 1.66.0 so that's been a minor hassle to resolve.  
  
Historically, it has been the non-Windows builds that have shown wild variation in filenames between Boost releases. I spent a few hours trying to make sense of [CMake's and Boost's file-naming history](https://gitlab.kitware.com/cmake/cmake/issues/17575#note_358583) yesterday with an aim to rationalising it.  
  
My personal use-case for this work is as a basis/known-good point for experimenting with beast on Standalone ASIO or the Networking TS reference implementation (I understand the latter is a subset of the former, and haven't looked yet to see if it's sufficient for beast's needs), partly to get practice with the Networking TS, and partly because I'm interested in using beast with an existing project where Boost isn't really practical to integrate at this time.  
  
Once the CMake support for Boost 1.66.0 on Windows hits AppVeyor, or Boost 1.66.0 itself starts to hit Linux packages, then I can (and probably will) get CI builds going for this branch. If there were available now, I'd have included them in the pull request. I could do AppVeyor right now, by including the updated `FindBoost.cmake` in the repo, but that seems ugly for a pull request.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2018-01-08 03:28:49 UTC  
> Url: https://github.com/boostorg/beast/pull/956#issuecomment-355878384  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/956?src=pr&el=h1) Report  
> Merging [#956](https://codecov.io/gh/boostorg/beast/pull/956?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/6f08814a0c291eb9f03aaa1daefffdc45c1b9087?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/956/graphs/tree.svg?token=Gq6MFA9JRF&src=pr&width=650&height=150)](https://codecov.io/gh/boostorg/beast/pull/956?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #956   +/-   ##  
========================================  
  Coverage    95.54%   95.54%             
========================================  
  Files          105      105             
  Lines        10543    10543             
========================================  
  Hits         10073    10073             
  Misses         470      470  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/956?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/956?src=pr&el=footer). Last update [6f08814...c0222af](https://codecov.io/gh/boostorg/beast/pull/956?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2018-02-15 03:23:01 UTC  
> Url: https://github.com/boostorg/beast/pull/956#issuecomment-365815542  

@djarek or @glenfe do you have any way to evaluate this?

---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2018-02-15 23:15:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/956#pullrequestreview-97040949  

📁 CMakeLists.txt

```diff
  55 |     set( CMAKE_CXX_FLAGS
  56 |-       "${CMAKE_CXX_FLAGS} -std=c++11 -Wall -Wextra -Wpedantic -Wno-unused-parameter")
  56 |+       "${CMAKE_CXX_FLAGS} -std=c++11 -Wall -Wextra -Wpedantic -Wno-unused-parameter -Wno-implicit-fallthrough")
```

> Username: vinniefalco  
> Created_at: 2018-02-15 23:15:13 UTC  
> Updated_at: 2018-02-15 23:15:14 UTC  
> Url: https://github.com/boostorg/beast/pull/956#discussion_r168637685  

I prefer not to turn off the warning, I believe the remaining warnings are fixed in 154.

> Username: TBBle  
> Created_at: 2018-03-02 08:21:35 UTC  
> Url: https://github.com/boostorg/beast/pull/956#discussion_r171785849  

Agreeded. It *was* warning at the time I posted this review, but definitely better to have fixed the warnings.

> Username: pfultz2  
> Created_at: 2018-05-26 00:00:26 UTC  
> Updated_at: 2018-05-26 00:00:27 UTC  
> Url: https://github.com/boostorg/beast/pull/956#discussion_r191032078  

The `CMAKE_CXX_FLAGS` should be set by the user(either from the command-line or in the toolchain file).  You can add flags for warnings with `add_compile_options`.  
  
Also, you shouldn't set the C++ version. The user should decide that. You could set a default with `CMAKE_CXX_STANDARD`.

> Username: vinniefalco  
> Created_at: 2018-05-26 00:37:06 UTC  
> Url: https://github.com/boostorg/beast/pull/956#discussion_r191034059  

But this CMakeLists.txt is for building the tests and examples, not for use for building a user's project that uses beast.

> Username: pfultz2  
> Created_at: 2018-05-26 04:16:32 UTC  
> Url: https://github.com/boostorg/beast/pull/956#discussion_r191039177  

But you want to be able build the tests and examples with a specific C++ version. There is semantic differences between the C++11 and C++17 that it makes sense to want to build the  tests and examples with C++17 instead of C++11.

> Username: vinniefalco  
> Created_at: 2018-05-26 13:27:56 UTC  
> Url: https://github.com/boostorg/beast/pull/956#discussion_r191048722  

I want to make sure that the tests and examples compile in C++11, so I don't accidentally use a C++14 or C++17 feature when I am maintaining beast.

> Username: pfultz2  
> Created_at: 2018-05-27 03:21:26 UTC  
> Url: https://github.com/boostorg/beast/pull/956#discussion_r191063493  

Yea but you have no way of testing for C++14 or C++17, either. Should users assume these are not supported since they are never tested for?   
  
Testing with C++11 will ensure everything will pass with C++11, but I dont see why that would negate testing with newer version as well.


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2018-02-15 23:16:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/956#pullrequestreview-97041208  

📁 CMakeLists.txt

```diff
  63 |+         # -O2 to work around string table size issues: https://stackoverflow.com/a/29479701/166389
  64 |+         set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wa,-mbig-obj -O2")
  65 |+     endif ()
```

> Username: vinniefalco  
> Created_at: 2018-02-15 23:16:25 UTC  
> Updated_at: 2018-02-15 23:16:26 UTC  
> Url: https://github.com/boostorg/beast/pull/956#discussion_r168637923  

This change looks benign, and could be accepted

> Username: TBBle  
> Created_at: 2018-05-27 13:28:01 UTC  
> Url: https://github.com/boostorg/beast/pull/956#discussion_r191076642  

That was why I kept it as a separate commit, since it would be needed even when using CMake in the way you intended/documented (albeit with gcc or clang under Win32, which is not a super-common workflow.)


---

## Review 8 [Commented]

> Username: djarek  
> Created_at: 2018-02-15 23:43:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/956#pullrequestreview-97046215  

📁 CMakeLists.txt

```diff
  87 |+ OPTION(FIND_BOOST "Build beast against an existing Boost install, rather than in-tree")
  88 |+ if (FIND_BOOST)
  89 |+   set(Boost_USE_STATIC_LIBS ON)
```

> Username: djarek  
> Created_at: 2018-02-15 23:41:23 UTC  
> Updated_at: 2018-02-15 23:43:33 UTC  
> Url: https://github.com/boostorg/beast/pull/956#discussion_r168642369  

Why are you forcing the use of static libs?

> Username: TBBle  
> Created_at: 2018-03-02 08:26:46 UTC  
> Url: https://github.com/boostorg/beast/pull/956#discussion_r171786640  

If I remember correctly, it was because linking against the dynamic libraries on Windows would then require the Boost DLLs for that version to be in your system path, or be copied into the library directory. The former doesn't appear to be the default, and the latter would normally be handled by `install` rules, and this CMakeLists.txt is not set up for the install stage, and I don't see there's any expectation that this will change.  
  
Basically, this gives the best out-of-the-box experience in common scenarios, where you can immediately run unit-tests (i.e. through CTest, which is a different patch series I haven't put up for merging) after the build. On Windows it'll link to static libs, and (again, if I remember correctly) on Linux you generally don't *have* static libs so it'll link to the dynamic libraries anyway.


---

## Comment 9

> Username: mloskot  
> Created_at: 2018-03-11 14:11:02 UTC  
> Url: https://github.com/boostorg/beast/pull/956#issuecomment-372118407  

Building and testing against a released Boost could be considered as an extension to this [official Boost recommendation](https://svn.boost.org/trac10/wiki/StartModMaint#Gettingreadytoworkonalibrary):  
  
>  The preferred environment library maintenance is to checkout the library's develop branch, or some other development branch, while other Boost libraries are as defined by the Boost super-project master branch. This causes local tests of your library to run against master for other Boost libraries at the point in time referenced by the Boost super-project.   
  
It could also simplify the procedure by allowing to clone just Beast, not the whole super project.

---

## Comment 10

> Username: TBBle  
> Created_at: 2018-03-23 01:11:18 UTC  
> Url: https://github.com/boostorg/beast/pull/956#issuecomment-375507353  

> It could also simplify the procedure by allowing to clone just Beast, not the whole super project.  
  
That's *precisely* what I wanted to do, that started me down this path.

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2018-03-23 02:04:01 UTC  
> Updated_at: 2018-03-23 02:04:21 UTC  
> Url: https://github.com/boostorg/beast/pull/956#issuecomment-375516069  

>It could also simplify the procedure by allowing to clone just Beast, not the whole super project.  
  
Is this not possible using bjam and a version of boost installed from a package?

---

## Comment 12

> Username: mloskot  
> Created_at: 2018-03-23 07:34:32 UTC  
> Url: https://github.com/boostorg/beast/pull/956#issuecomment-375568030  

@TBBle I have adopted your concept for Boost.GIL. Thanks!  
@vinniefalco No idea, but developing a Boost library using IDE-friendly build setup than BBv2 simply is a winner

---

## Comment 13

> Username: stale[bot]  
> Created_at: 2018-04-24 16:52:16 UTC  
> Url: https://github.com/boostorg/beast/pull/956#issuecomment-384003447  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 14

> Username: TBBle  
> Created_at: 2018-04-25 07:00:09 UTC  
> Url: https://github.com/boostorg/beast/pull/956#issuecomment-384180922  

I'd characterise it as "Not of particular interest to the maintainer", and my original motivation (Beast using Standalone ASIO in a non-Boost environment) is no longer applicable.

---

## Comment 15

> Username: stale[bot]  
> Created_at: 2018-05-25 07:56:27 UTC  
> Url: https://github.com/boostorg/beast/pull/956#issuecomment-391973261  

This issue has been open for a while with no activity, has it been resolved?

---

## Review 16 [Commented]

> Username: pfultz2  
> Created_at: 2018-05-26 00:00:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/956#pullrequestreview-123538997  

📁 CMakeLists.txt

```diff
  89 |+   set(Boost_USE_STATIC_LIBS ON)
  90 |+   find_package(Boost 1.66.0 REQUIRED COMPONENTS system filesystem coroutine)
  91 |+   include_directories(${Boost_INCLUDE_DIRS})
```

> Username: pfultz2  
> Created_at: 2018-05-26 00:00:55 UTC  
> Updated_at: 2018-05-26 00:00:56 UTC  
> Url: https://github.com/boostorg/beast/pull/956#discussion_r191032101  

Usage requirements should be applied to a target.

> Username: TBBle  
> Created_at: 2018-05-27 13:37:17 UTC  
> Url: https://github.com/boostorg/beast/pull/956#discussion_r191076897  

I feel a lot of your comments here are directed at the code as it already stood, rather than this pull request, and probably should be handled in a separate (possibly preceding) change.  
  
For this series, I didn't want to rewrite the CMake build system, simply extend what was already there to accommodate a different workflow. @vinniefalco has expressed reluctance to take on the workload of a full CMake-based build system, given the minimal benefits it provides to him as a maintainer, and almost-zero benefits it provides to users of this library (who would get it via Boost and hence never interact with this build-system at all).  
  
Similarly, I wasn't particularly interested in trying to full CMake-ify this code, for the same reasons. If and when BCM or similar lands in Boost, then it's worth revisiting this, as the potential maintainer load would be much lower, and the user-visible benefits much higher.


---

## Review 17 [Commented]

> Username: pfultz2  
> Created_at: 2018-05-26 00:06:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/956#pullrequestreview-123539340  

📁 CMakeLists.txt

```diff
  86 | 
  84 |- include_directories (${BOOST_ROOT})
  87 |+ OPTION(FIND_BOOST "Build beast against an existing Boost install, rather than in-tree")
```

> Username: pfultz2  
> Created_at: 2018-05-26 00:06:01 UTC  
> Url: https://github.com/boostorg/beast/pull/956#discussion_r191032402  

Ugly but necessary workaround until boost officially supports cmake. In Boost.CMake, the projects just unashamedly use `find_package`, but the superproject disables the in-tree versions with [`bcm_ignore_package`](http://bcm.readthedocs.io/en/latest/src/BCMIgnorePackage.html#bcm-ignore-package).


---

## Comment 18

> Username: vinniefalco  
> Created_at: 2018-05-26 13:28:44 UTC  
> Url: https://github.com/boostorg/beast/pull/956#issuecomment-392261552  

> I'd characterise it as "Not of particular interest to the maintainer",  
  
I am interested but this is a seriously complex problem.

---

## Comment 19

> Username: stale[bot]  
> Created_at: 2018-06-26 13:45:01 UTC  
> Url: https://github.com/boostorg/beast/pull/956#issuecomment-400314036  

This issue has been open for a while with no activity, has it been resolved?

---
