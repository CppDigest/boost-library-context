# #781 test: fix CMake issue with unusual compilers [Merged]

> Username: iskunk  
> Created at: 2023-10-05 19:56:46 UTC  
> Updated at: 2023-10-06 16:02:12 UTC  
> Merged at: 2023-10-06 16:02:12 UTC  
> Closed at: 2023-10-06 16:02:12 UTC  
> Url: https://github.com/boostorg/url/pull/781  

I am building Boost using CMake along with the Intel C++ compiler. I was seeing this error at configuration time:  
```  
-- Performing Test BOOST_STACKTRACE_HAS_WINDBG - Failed  
-- Performing Test BOOST_STACKTRACE_HAS_WINDBG_CACHED  
-- Performing Test BOOST_STACKTRACE_HAS_WINDBG_CACHED - Failed  
-- Boost.Stacktrace: noop ON, backtrace OFF, addr2line ON, basic ON, windbg OFF, windbg_cached OFF  
-- Boost.Thread: threading API is pthread  
CMake Error at libs/url/test/unit/CMakeLists.txt:19 (set_source_files_properties):  
  set_source_files_properties called with incorrect number of arguments.  
  
  
CMake Error at libs/url/test/limits/CMakeLists.txt:19 (set_source_files_properties):  
  set_source_files_properties called with incorrect number of arguments.  
  
  
-- Configuring incomplete, errors occurred!  
See also "/tmp/boost-build/CMakeFiles/CMakeOutput.log".  
See also "/tmp/boost-build/CMakeFiles/CMakeError.log".  
```  
  
The problem is in the `Replicate error flags from Jamfile` section in `url/test/CMakeLists.txt`. My compiler has a `CMAKE_CXX_COMPILER_ID` value of `Intel`. This is not in the list of cases handled in the CMake code, so `BOOST_URL_TEST_FLAGS` remains unset. This causes the aforementioned `set_source_files_properties()` calls to not receive the arguments they need.  
  
This change is one way of fixing the issue. It gets the build working again for me.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-10-05 20:19:37 UTC  
> Updated_at: 2023-10-06 03:42:41 UTC  
> Url: https://github.com/boostorg/url/pull/781#issuecomment-1749586187  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/781?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#781](https://app.codecov.io/gh/boostorg/url/pull/781?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (72f4882) into [develop](https://app.codecov.io/gh/boostorg/url/commit/1dc789839f34f9a13451c3aaa5bf4cacec04fd78?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1dc7898) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/781/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/781?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #781   +/-   ##  
========================================  
  Coverage    97.27%   97.27%             
========================================  
  Files          156      156             
  Lines         8565     8565             
========================================  
  Hits          8332     8332             
  Misses         233      233             
```  
  
  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/781?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/781?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1dc7898...72f4882](https://app.codecov.io/gh/boostorg/url/pull/781?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-10-05 20:23:41 UTC  
> Url: https://github.com/boostorg/url/pull/781#issuecomment-1749590686  

GCOVR code coverage report [https://781.url.prtest2.cppalliance.org/gcovr/index.html](https://781.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://781.url.prtest2.cppalliance.org/genhtml/index.html](https://781.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://781.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://781.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 3

> Username: alandefreitas  
> Created_at: 2023-10-06 01:15:00 UTC  
> Updated_at: 2023-10-06 01:15:34 UTC  
> Url: https://github.com/boostorg/url/pull/781#issuecomment-1749858303  

True. I ended up not giving #777 proper attention.   
  
Doesn't just adding  
  
```  
set(BOOST_URL_TEST_FLAGS "")  
````  
  
as the default condition before the flag setting section work?  
  
The semantics would make more sense and it would be less code.

---

## Comment 4

> Username: iskunk  
> Created_at: 2023-10-06 03:17:30 UTC  
> Url: https://github.com/boostorg/url/pull/781#issuecomment-1749928693  

Setting the variable to a default of `""` does not work, but I've confirmed that setting it to `" "` (a space) is enough to satisfy `set_source_files_properties()`. Updated the patch accordingly.

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-10-06 03:42:32 UTC  
> Url: https://github.com/boostorg/url/pull/781#issuecomment-1749942017  

GCOVR code coverage report [https://781.url.prtest2.cppalliance.org/gcovr/index.html](https://781.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://781.url.prtest2.cppalliance.org/genhtml/index.html](https://781.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://781.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://781.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 6

> Username: alandefreitas  
> Created_at: 2023-10-06 05:21:46 UTC  
> Updated_at: 2023-10-06 05:22:16 UTC  
> Url: https://github.com/boostorg/url/pull/781#issuecomment-1750000489  

> Setting the variable to a default of "" does not work, but I've confirmed that setting it to " " (a space) is enough to satisfy set_source_files_properties(). Updated the patch accordingly.  
  
Interesting. Yes. It does make sense. Nice.

---
