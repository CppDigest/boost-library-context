# #86 Implemented BOOST_PFR_ENABLED macro [Merged]

> Username: denzor200  
> Created at: 2021-06-20 12:32:54 UTC  
> Updated at: 2025-12-04 19:59:20 UTC  
> Merged at: 2022-12-18 19:51:01 UTC  
> Closed at: 2022-12-18 19:51:01 UTC  
> Url: https://github.com/boostorg/pfr/pull/86  

New macro tested on the Godbolt in so many variants:  
case 1 (before) : https://godbolt.org/z/hjhKf3h5n  
case 1 (after) : https://godbolt.org/z/vfeKfcMh7  
case 2 (before) : https://godbolt.org/z/bvfocY1v1  
case 2 (after) : https://godbolt.org/z/bnYboGYa5  
case 3 (before) : https://godbolt.org/z/es7cEoo75  
case 3 (after) : https://godbolt.org/z/sMdr1vGPW

---

## Comment 1

> Username: coveralls  
> Created_at: 2021-06-20 12:48:41 UTC  
> Url: https://github.com/boostorg/pfr/pull/86#issuecomment-864549431  

## Pull Request Test Coverage Report for [Build 954428715](https://coveralls.io/builds/40724497)  
  
* **0** of **0**   changed or added relevant lines in **0** files are covered.  
* No unchanged relevant lines lost coverage.  
* Overall coverage remained the same at **100.0%**  
  
---  
  
  
  
|  Totals | [![Coverage Status](https://coveralls.io/builds/40724497/badge)](https://coveralls.io/builds/40724497) |  
| :-- | --: |  
| Change from base [Build 931559387](https://coveralls.io/builds/40534615): |  0.0% |  
| Covered Lines: | 332 |  
| Relevant Lines: | 332 |  
  
---  
##### 💛  - [Coveralls](https://coveralls.io)

---

## Comment 2

> Username: coveralls  
> Created_at: 2021-06-20 12:48:41 UTC  
> Updated_at: 2025-12-04 19:59:20 UTC  
> Url: https://github.com/boostorg/pfr/pull/86#issuecomment-864549432  

## Pull Request Test Coverage Report for [Build 3593658456](https://coveralls.io/builds/54750727)  
  
### Warning: This coverage report may be inaccurate.  
  
This pull request's base commit is no longer the HEAD commit of its target branch. This means it includes changes from outside the original pull request, including, potentially, unrelated coverage changes.  
  
- For more information on this, see <a target="_blank" href="https://docs.coveralls.io/build-types#tracking-coverage-changes-with-pull_request-builds">Tracking coverage changes with pull request builds</a>.  
- To avoid this issue with future PRs, see these <a target="_blank" href="https://docs.coveralls.io/build-types#recommended-ci-configurations">Recommended CI Configurations</a>.  
- For a quick fix, <a target="_blank" href="https://github.blog/changelog/2022-02-03-more-ways-to-keep-your-pull-request-branch-up-to-date/#update-your-pull-request-branch-by-rebasing">rebase this PR at GitHub</a>. Your next report should be accurate.  
  
### Details  
  
* **0** of **0**   changed or added relevant lines in **0** files are covered.  
* No unchanged relevant lines lost coverage.  
* Overall coverage remained the same at **100.0%**  
  
---  
  
  
  
|  Totals | [![Coverage Status](https://coveralls.io/builds/54750727/badge)](https://coveralls.io/builds/54750727) |  
| :-- | --: |  
| Change from base [Build 3117643244](https://coveralls.io/builds/52730529): |  0.0% |  
| Covered Lines: | 337 |  
| Relevant Lines: | 337 |  
  
---  
##### 💛  - [Coveralls](https://coveralls.io)

---

## Comment 3

> Username: denzor200  
> Created_at: 2021-06-20 19:15:25 UTC  
> Updated_at: 2021-06-20 19:15:34 UTC  
> Url: https://github.com/boostorg/pfr/pull/86#issuecomment-864599402  

I think this is a bad idea.. But we will not close it yet

---

## Review 4 [Commented]

> Username: denzor200  
> Created_at: 2022-12-02 14:12:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/86#pullrequestreview-1202557990  

📁 include/boost/pfr/config.hpp

```diff
  92 |+ #endif
  93 |+ 
  94 |+ #ifndef BOOST_NO_PFR
```

> Username: denzor200  
> Created_at: 2022-12-02 14:11:44 UTC  
> Updated_at: 2022-12-02 14:12:05 UTC  
> Url: https://github.com/boostorg/pfr/pull/86#discussion_r1038181974  

Add `BOOST_NO_PFR` macro into:  
-doc/pfr.qbk (near another macroses)  
-test/print_config.cpp (and change inclusion core.hpp -> config.hpp)


---

## Comment 5

> Username: denzor200  
> Created_at: 2022-12-10 19:55:15 UTC  
> Url: https://github.com/boostorg/pfr/pull/86#issuecomment-1345372499  

> I think this is a bad idea.. But we will not close it yet  
  
This idea may become good if we provide the way to override compiler's decision manually. Like this:  
```  
#define BOOST_NO_PFR 1    //< force set to 'disallowed'  
#include <boost/pfr.hpp>  //< compile time error, PFR not supported by user's decision  
```  
```  
#define BOOST_NO_PFR 0    //< force set to 'allowed'  
#include <boost/pfr.hpp>  //< good when your compiler really supports pfr  
```  
```  
// #define BOOST_NO_PFR 0  //< let compiler set PFR's status  
#include <boost/pfr.hpp>   //< good when your compiler support pfr  
```  
  
I'll continue working on it.

---

## Review 6 [Commented]

> Username: denzor200  
> Created_at: 2022-12-10 19:58:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/86#pullrequestreview-1212612550  

📁 include/boost/pfr/detail/config.hpp

```diff
  14 |+ #error Boost.PFR library is not supported in your environment.             \
  15 |+        Try one of the possible solutions:                                  \
  16 |+        1. enable C++14;                                                    \
```

> Username: denzor200  
> Created_at: 2022-12-10 19:57:41 UTC  
> Updated_at: 2022-12-10 19:58:06 UTC  
> Url: https://github.com/boostorg/pfr/pull/86#discussion_r1045130719  

First, we should propose user to take away an `-DBOOST_NO_PFR=1`, if it exist.  
Then all another.


---

## Review 7 [Changes requested]

> Username: apolukhin  
> Created_at: 2022-12-18 17:33:26 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/pfr/pull/86#pullrequestreview-1222064100  

📁 doc/pfr.qbk

```diff
 470 |     [[*BOOST_PFR_HAS_GUARANTEED_COPY_ELISION*] [Define to `0` if your compiler does not implement C++17 guaranteed copy elision properly and fails to reflect aggregates with non-movable fields. Define to `1` to override Boost.PFR detection logic. ]]
 471 |     [[*BOOST_PFR_ENABLE_IMPLICIT_REFLECTION*] [Define to `0` if you are hit by lots of non-effective choices made by implicitly reflection. Define to `1` to override Boost.PFR detection logic. ]]
 472 |+     [[*BOOST_NO_PFR*] [On platforms where Boost.PFR is not supported, the boost/pfr/config.hpp header defines the BOOST_NO_PFR macro equals 1. Defining this macro as 1 before including the header disables the Boost.PFR library. ]]
```

> Username: apolukhin  
> Created_at: 2022-12-18 17:14:38 UTC  
> Updated_at: 2022-12-18 17:33:26 UTC  
> Url: https://github.com/boostorg/pfr/pull/86#discussion_r1051637622  

```diff  
-    [[*BOOST_NO_PFR*] [On platforms where Boost.PFR is not supported, the boost/pfr/config.hpp header defines the BOOST_NO_PFR macro equals 1. Defining this macro as 1 before including the header disables the Boost.PFR library. ]]  
+    [[*BOOST_NO_PFR*] [On platforms where Boost.PFR is not supported, the `boost/pfr/config.hpp` header defines the BOOST_NO_PFR macro equal to 1. Defining this macro as 1 before including the header disables the Boost.PFR library. ]]  
```

---

📁 doc/pfr.qbk

```diff
 470 |     [[*BOOST_PFR_HAS_GUARANTEED_COPY_ELISION*] [Define to `0` if your compiler does not implement C++17 guaranteed copy elision properly and fails to reflect aggregates with non-movable fields. Define to `1` to override Boost.PFR detection logic. ]]
 471 |     [[*BOOST_PFR_ENABLE_IMPLICIT_REFLECTION*] [Define to `0` if you are hit by lots of non-effective choices made by implicitly reflection. Define to `1` to override Boost.PFR detection logic. ]]
 472 |+     [[*BOOST_NO_PFR*] [On platforms where Boost.PFR is not supported, the boost/pfr/config.hpp header defines the BOOST_NO_PFR macro equals 1. Defining this macro as 1 before including the header disables the Boost.PFR library. ]]
```

> Username: apolukhin  
> Created_at: 2022-12-18 17:15:02 UTC  
> Updated_at: 2022-12-18 17:33:26 UTC  
> Url: https://github.com/boostorg/pfr/pull/86#discussion_r1051637694  

Please change the file permossin back to non-executable


📁 include/boost/pfr/detail/config.hpp

```diff
  10 | 
  10 |- #include <type_traits> // to get non standard platform macro definitions (__GLIBCXX__ for example)
  11 |+ #include <boost/pfr/config.hpp>
```

> Username: apolukhin  
> Created_at: 2022-12-18 17:17:53 UTC  
> Updated_at: 2022-12-18 17:33:26 UTC  
> Url: https://github.com/boostorg/pfr/pull/86#discussion_r1051637978  

Please change the file permission back to non-executable

---

📁 include/boost/pfr/detail/config.hpp

```diff
  15 |- //  * MSVC++ 14.0 _MSC_VER == 1900 (Visual Studio 2015)
  16 |- //  * MSVC++ 12.0 _MSC_VER == 1800 (Visual Studio 2013)
  13 |+ #if BOOST_NO_PFR
```

> Username: apolukhin  
> Created_at: 2022-12-18 17:32:37 UTC  
> Updated_at: 2022-12-18 17:33:26 UTC  
> Url: https://github.com/boostorg/pfr/pull/86#discussion_r1051639525  

Negation in macro is not nice. How about changing the macro name to `BOOST_PFR_ENABLED`?


📁 test/print_config.cpp

```diff
   4 | // file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)
   5 | 
   6 |- #include <boost/pfr/core.hpp>
```

> Username: apolukhin  
> Created_at: 2022-12-18 17:33:22 UTC  
> Updated_at: 2022-12-18 17:33:26 UTC  
> Url: https://github.com/boostorg/pfr/pull/86#discussion_r1051639583  

chmod -x


---
