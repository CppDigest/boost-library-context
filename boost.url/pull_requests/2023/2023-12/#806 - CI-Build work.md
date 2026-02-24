# #806 CI/Build work [Merged]

> Username: alandefreitas  
> Created at: 2023-12-21 22:52:27 UTC  
> Updated at: 2024-02-21 00:54:35 UTC  
> Merged at: 2023-12-22 15:05:54 UTC  
> Closed at: 2023-12-22 15:05:54 UTC  
> Url: https://github.com/boostorg/url/pull/806  

This PR fixes several problems with our build scripts that have been revealed after many recent updates in the GitHub actions we use.  
  
- All tests depend on the Boost custom `tests` target  (#804). Tests were depending on Boost.URL specific targets that don't match the convention for Boost libraries.  
- Readjust flags for tests on all build scripts. The logic for test flags in cmake also needs to check `CMAKE_CXX_COMPILER_FRONTEND_VARIANT`. In particular, if the compiler is clang but the frontend is MSVC, we need to add flags as if it were MSVC rather than GCC-like flags. This commit also updates existing flags to account for new compiler versions.  
- Logic for building tests relies exclusively on BUILD_TESTING (PR #805).  When URL was configured with cmake `-D BUILD_TESTING=ON` and then reconfigured with cmake `-D BUILD_TESTING=OFF`, tests would remain enabled. That's because `BUILD_TESTING` was only used once as the default value of `BOOST_URL_BUILD_TESTS` being cached. That's not how all other Boost libraries work. This commit makes tests rely on `BUILD_TESTING` and, for backwards compatibility, `BOOST_URL_BUILD_TESTS` is only available as an extra option to enable Boost.URL tests even if `BUILD_TESTING` is `OFF`.  
- Build scripts set `CMAKE_RUNTIME_OUTPUT_DIRECTORY` when building with Boost.URL as the root project. This fixes a problem with shared libraries, which were failing CI previously but we weren't catching this in the tests.  
- The CMake integration workflows are aggregated into a single step. CMake parameters that are common to all steps are also aggregated with environment variables, which was made possible after moving the underlying action to JavaScript. This makes the current workflow much easier to read.  
  
These changes required us to update cpp-actions from v1.6.2 to [v1.7.0](https://github.com/alandefreitas/cpp-actions/releases/tag/v1.7.0). This release includes many changes that we noticed were influencing Boost.URL and Boost.Http but had to be fixed over there. Besides a few bug fixes, it includes:  
  
- An option to create lists of extra arguments as factors for cmake-workflows (which we needed to aggregate the cmake integration workflows),   
- The generator toolset option (which we needed to make `clang-cl` tests work)  
- Individual options for B2-specific options (which we needed for `rtti` and `abbreviate-paths`)  
- Better defaults for the matrix (some defaults were wrong for less common compilers)

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-12-21 23:15:18 UTC  
> Url: https://github.com/boostorg/url/pull/806#issuecomment-1867041374  

GCOVR code coverage report [https://806.url.prtest2.cppalliance.org/gcovr/index.html](https://806.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://806.url.prtest2.cppalliance.org/genhtml/index.html](https://806.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://806.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://806.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-12-21 23:15:23 UTC  
> Updated_at: 2023-12-21 23:15:30 UTC  
> Url: https://github.com/boostorg/url/pull/806#issuecomment-1867041441  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/806?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`b8b9140`)](https://app.codecov.io/gh/boostorg/url/commit/b8b91407fdea3d63517786cd174929ed59138595?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 97.27% compared to head [(`be67f77`)](https://app.codecov.io/gh/boostorg/url/pull/806?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 97.27%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/806/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/806?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #806   +/-   ##  
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
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/806?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/806?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b8b9140...be67f77](https://app.codecov.io/gh/boostorg/url/pull/806?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
