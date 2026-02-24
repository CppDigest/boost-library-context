# #199 cmake: cmake_minimum_required 3.10 (3.9 is deprecated for cmake 3.31 onwards) [Closed]

> Username: nigels-com  
> Created at: 2025-09-05 05:07:23 UTC  
> Updated at: 2025-09-07 11:39:35 UTC  
> Closed at: 2025-09-07 08:54:00 UTC  
> Url: https://github.com/boostorg/nowide/pull/199  

The current version of make is 4.1.1  
  
It's complaining that cmake 3.9 policy [is deprecated](https://cmake.org/cmake/help/latest/command/cmake_minimum_required.html#policy-version) since 3.31  
  
This change brings the minimum cmake requirement to 3.10 (Nov 2017).  
  
Boost more broadly needs cmake 3.20 (Apr 2021) or newer to build monolithically.

---

## Comment 1

> Username: Flamefire  
> Created_at: 2025-09-05 10:12:18 UTC  
> Url: https://github.com/boostorg/nowide/pull/199#issuecomment-3257820914  

> Boost more broadly needs cmake 3.20 (Apr 2021) or newer to build monolithically.  
  
Why is that? What is the limiting factor for earlier versions?  
  
3.10 Seems reasonable as that is what is available in Ubuntu 18 repos

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-09-05 12:30:13 UTC  
> Updated_at: 2025-09-07 08:54:08 UTC  
> Url: https://github.com/boostorg/nowide/pull/199#issuecomment-3258185691  

## [Codecov](https://app.codecov.io/gh/boostorg/nowide/pull/199?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 97.73%. Comparing base ([`7ba7e46`](https://app.codecov.io/gh/boostorg/nowide/commit/7ba7e46a6a96eab60379a9656792f5b007dc4559?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`dd4f7da`](https://app.codecov.io/gh/boostorg/nowide/commit/dd4f7da1accd737b5a645f859e8f287a54d430d1?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #199      +/-   ##  
===========================================  
- Coverage    99.10%   97.73%   -1.37%       
===========================================  
  Files           34       34                
  Lines         3591     3052     -539       
===========================================  
- Hits          3559     2983     -576       
- Misses          32       69      +37       
```  
[see 31 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/nowide/pull/199/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 3

> Username: nigels-com  
> Created_at: 2025-09-05 23:39:16 UTC  
> Url: https://github.com/boostorg/nowide/pull/199#issuecomment-3260058378  

> Why is that? What is the limiting factor for earlier versions?  
  
Good question.  I dug some more.  
  
Cobalt (cmake-3.10.0 fails, cmake-3.12.0 succeeds)  
  
```  
$ git clean -xdf . && PATH=/opt/cmake-3.10.0-Linux-x86_64/bin:$PATH cmake .. -G Ninja -DCMAKE_BUILD_TYPE=Release -DBUILD_TESTING=N -DCMAKE_CXX_COMPILER=clang++-19 -DBOOST_INCLUDE_LIBRARIES=cobalt && ninja  
...  
-- Boost installation support requires CMake 3.13 (have 3.10.0)  
CMake Error at libs/cobalt/CMakeLists.txt:7 (cmake_minimum_required):  
  CMake 3.12...3.20 or higher is required.  You are running version 3.10.0  
```  
  
Heap (cmake-3.18.0 fails, cmake-3.19.0 succeeds)  
  
```  
$ git clean -xdf . && PATH=/opt/cmake-3.12.0-Linux-x86_64/bin:$PATH cmake .. -G Ninja -DCMAKE_BUILD_TYPE=Release -DBUILD_TESTING=N -DCMAKE_CXX_COMPILER=clang++-19 -DBOOST_INCLUDE_LIBRARIES=heap && ninja  
...  
-- Boost installation support requires CMake 3.13 (have 3.12.0)  
CMake Error at libs/heap/CMakeLists.txt:22 (set_target_properties):  
  INTERFACE_LIBRARY targets may only have whitelisted properties.  The  
  property "CMAKE_CXX_STANDARD_REQUIRED" is not allowed.  
```  
  
> Boost more broadly needs cmake 3.20 (Apr 2021) or newer to build monolithically.  
  
cmake 3.19.0 (Nov 2020) does also work monolithically.  
  
Are you aware of some particular cmake baseline version that is _supposed_ to be supported?  
Looks like some build coverage could be added, or the alphas could be scrutinized manually.  
I'm generally happy to use a recent cmake, but that is an extra hurdle for newcomers though.

---

## Comment 4

> Username: Flamefire  
> Created_at: 2025-09-06 11:13:15 UTC  
> Url: https://github.com/boostorg/nowide/pull/199#issuecomment-3261887309  

>   
> Cobalt (cmake-3.10.0 fails, cmake-3.12.0 succeeds)  
>   
> ```  
> CMake Error at libs/cobalt/CMakeLists.txt:7 (cmake_minimum_required):  
>   CMake 3.12...3.20 or higher is required.  You are running version 3.10.0  
> ```  
> Heap (cmake-3.18.0 fails, cmake-3.19.0 succeeds)  
>   
> ```  
> $ git clean -xdf . && PATH=/opt/cmake-3.12.0-Linux-x86_64/bin:$PATH cmake .. -G Ninja -DCMAKE_BUILD_TYPE=Release -DBUILD_TESTING=N -DCMAKE_CXX_COMPILER=clang++-19 -DBOOST_INCLUDE_LIBRARIES=heap && ninja  
> ...  
> -- Boost installation support requires CMake 3.13 (have 3.12.0)  
> CMake Error at libs/heap/CMakeLists.txt:22 (set_target_properties):  
>   INTERFACE_LIBRARY targets may only have whitelisted properties.  The  
>   property "CMAKE_CXX_STANDARD_REQUIRED" is not allowed.  
> ```  
  
Ok so Cobalt does require 3.12, Heap does have a bug in its CMakeLists which likely will be fixed in the next version.  
  
> cmake 3.19.0 (Nov 2020) does also work monolithically.  
  
That is only due to CMake 3.19 ignoring the bug mentioned above.  
  
> Are you aware of some particular cmake baseline version that is _supposed_ to be supported?  
  
I'm not aware of that as there is no Boost-wide rule to support a specific minimum version.  
As installation support requires 3.13 using that is a safe bet, once the bug fixes are merged.  
  
>Looks like some build coverage could be added, or the alphas could be scrutinized manually. I'm generally happy to use a recent cmake, but that is an extra hurdle for newcomers though.  
  
To still allow 3.9 in general for Nowide I made a [different PR](https://github.com/boostorg/nowide/pull/200) that also fixes you issue.  
  
Note that CMake build support is still experimental so some issues might still need to be fixed

---

## Comment 5

> Username: nigels-com  
> Created_at: 2025-09-06 21:42:56 UTC  
> Url: https://github.com/boostorg/nowide/pull/199#issuecomment-3263237515  

To clarify.  
  
This PR #199 emits no warning for cmake-3.31.0.  
  
The alternative PR #200 emits the warning:  
  
```  
$ PATH=/opt/cmake-3.31.0-linux-x86_64/bin:$PATH cmake .. -G Ninja -DCMAKE_BUILD_TYPE=Release -DBUILD_TESTING=Y -DCMAKE_CXX_COMPILER=clang++-19 -DBOOST_INCLUDE_LIBRARIES=nowide && ninja  
...  
CMake Deprecation Warning at libs/nowide/CMakeLists.txt:16 (cmake_minimum_required):  
  Compatibility with CMake < 3.10 will be removed from a future version of  
  CMake.  
  
  Update the VERSION argument <min> value or use a ...<max> suffix to tell  
  CMake that the project does not need compatibility with older versions.  
```  
  
My goal here is to resolve the cmake deprecation warning.

---

## Comment 6

> Username: Flamefire  
> Created_at: 2025-09-07 08:56:43 UTC  
> Url: https://github.com/boostorg/nowide/pull/199#issuecomment-3263600853  

> The alternative PR #200 emits the warning:  
  
Thanks for testing!     
Turns out the version range specifier should have been "..." not "..". With the fixed commit this works now and I merged that change. Can you double check the current develop branch on your side?

---

## Comment 7

> Username: nigels-com  
> Created_at: 2025-09-07 11:39:35 UTC  
> Url: https://github.com/boostorg/nowide/pull/199#issuecomment-3263712886  

Confirming that.  Thanks for the fix.

---
