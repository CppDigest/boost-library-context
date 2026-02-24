# #741 Simplify GHA CI [Merged]

> Username: alandefreitas  
> Created at: 2023-04-20 21:07:24 UTC  
> Updated at: 2023-07-24 14:37:52 UTC  
> Merged at: 2023-04-21 07:36:04 UTC  
> Closed at: 2023-04-21 07:36:04 UTC  
> Url: https://github.com/boostorg/url/pull/741  

This PR extremely simplifies our GHA workflow. The motivation is a previous PR where some bugs didn't get caught because they weren't being tested. The workflow had become so complex that it effectively became a black box where it was hard to identify problems. Some tasks were executed twice, some were incoherent, and some were missing.  
  
The workflow in this PR is extremely simplified into one optional action per step: patch boost, cmake workflow, or b2 workflow. This is much more readable and any complex details are handled by the actions, which include their own steps with relevant optimizations.   
  
Assuming runners are available, which they usually are, the final result is also more efficient than our current drone workflows even with the public-hosted runners:  
  
![workflows](https://user-images.githubusercontent.com/5369819/233483942-f79e5abe-64e9-4b06-a59c-c923bebd9249.png)  
  
It might look like the complexity is only being moved to the actions files here, but the intention is to later move these actions to a separate repository, from where they can be reused. It's already the 4th repository where I use these actions and a new repository will be created for them once they are battle tested. So these are the actions:  
  
- The `boost_clone` action includes a script to scan dependencies before cloning boost and caching based only on these dependencies. Caching saves us about 4 minutes in the workflow. When there's no cache, the scanning scripting saves us about 2 minutes.  
- The `b2_workflow` action takes the boost source directory and does whatever it needs to test the specified modules. This includes compiling `b2` if needed and generating a proper `user-config.jam` file.  
- The `cmake_workflow` validates the cmake version installed in the system, updates cmake if the library has a different minimum version, identifies what features that cmake version supports, and runs a complete cmake workflow (configure, build, test, install) adjusting the parameters as needed according to the features that cmake version supports. It also creates github annotations when warnings or errors are emitted at any of these steps.  
  
Here are some examples of annotations:  
  
![image](https://user-images.githubusercontent.com/5369819/233496499-8d4d3745-25ea-4440-8458-3014f5e6cec9.png)  
  
![image](https://user-images.githubusercontent.com/5369819/233496661-120554e9-4d99-4842-aa5f-30d5094da920.png)  
  
![image](https://user-images.githubusercontent.com/5369819/233496776-ddad40ac-8c5c-44cf-9365-e163fd5e6a3d.png)  
  
![image](https://user-images.githubusercontent.com/5369819/233496922-879d9b07-0de9-4967-ad2a-20a4ec100790.png)  
  
  
A few adaptations were made in the CMake scripts to make these workflows simpler and more efficient.  
  
- The main CMakeLists.txt was split into sections that are smaller and easier to read.  
- A separate section is dedicated to looking for Boost source files in `BOOST_SRC_DIR`, which still defaults to `../..`. The section implements the logic to create any interface targets Boost.URL might need. This means anything after this section can assume Boost was found from "../..", and any logic related to find_package(Boost) or any other way of finding boost was been completely removed.  
- The standalone install target has been removed. It never worked and we just didn't know about it because it wasn't being tested properly.   
- Other internal CMakeLists.txt files were standardized so they all look the same.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-04-20 21:28:26 UTC  
> Url: https://github.com/boostorg/url/pull/741#issuecomment-1516969314  

GCOVR code coverage report [https://741.url.prtest.cppalliance.org/gcovr/index.html](https://741.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://741.url.prtest.cppalliance.org/genhtml/index.html](https://741.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: alandefreitas  
> Created_at: 2023-04-20 21:56:46 UTC  
> Url: https://github.com/boostorg/url/pull/741#issuecomment-1516991787  

I just noticed something. The GHA workflows are faster but only as long as the machines become available. I noticed it never takes more than a few seconds when working on it but it usually takes several minutes once it's in `boostorg`. We will never know for sure, but it seems like GitHub implements some kind of load balancing weighted by profiles. So `boostorg` is just always consuming its public quota with hundreds of repositories testing with dozens of machines and even scheduled tasks sometimes.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2023-04-20 22:05:13 UTC  
> Updated_at: 2023-04-21 06:40:42 UTC  
> Url: https://github.com/boostorg/url/pull/741#issuecomment-1516999505  

## [Codecov](https://codecov.io/gh/boostorg/url/pull/741?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#741](https://codecov.io/gh/boostorg/url/pull/741?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a876470) into [develop](https://codecov.io/gh/boostorg/url/commit/963bf5a4b72cfb076838c67c36fc18a6be08181d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (963bf5a) will **increase** coverage by `0.02%`.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head a876470 differs from pull request most recent head 7fe70b2. Consider uploading reports for the commit 7fe70b2 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/741/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/741?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #741      +/-   ##  
===========================================  
+ Coverage    97.22%   97.25%   +0.02%       
===========================================  
  Files          155      155                
  Lines         8517     8517                
===========================================  
+ Hits          8281     8283       +2       
+ Misses         236      234       -2       
```  
  
  
[see 1 file with indirect coverage changes](https://codecov.io/gh/boostorg/url/pull/741/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://codecov.io/gh/boostorg/url/pull/741?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/741?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [963bf5a...7fe70b2](https://codecov.io/gh/boostorg/url/pull/741?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-04-20 22:06:41 UTC  
> Url: https://github.com/boostorg/url/pull/741#issuecomment-1517000814  

GCOVR code coverage report [https://741.url.prtest.cppalliance.org/gcovr/index.html](https://741.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://741.url.prtest.cppalliance.org/genhtml/index.html](https://741.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-04-20 22:42:39 UTC  
> Url: https://github.com/boostorg/url/pull/741#issuecomment-1517028822  

GCOVR code coverage report [https://741.url.prtest.cppalliance.org/gcovr/index.html](https://741.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://741.url.prtest.cppalliance.org/genhtml/index.html](https://741.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2023-04-21 00:23:21 UTC  
> Url: https://github.com/boostorg/url/pull/741#issuecomment-1517091299  

What happens when you build the Visual Studio solution using the VS2022 generator?

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-04-21 01:32:49 UTC  
> Url: https://github.com/boostorg/url/pull/741#issuecomment-1517133019  

GCOVR code coverage report [https://741.url.prtest.cppalliance.org/gcovr/index.html](https://741.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://741.url.prtest.cppalliance.org/genhtml/index.html](https://741.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-04-21 02:07:17 UTC  
> Url: https://github.com/boostorg/url/pull/741#issuecomment-1517155453  

GCOVR code coverage report [https://741.url.prtest.cppalliance.org/gcovr/index.html](https://741.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://741.url.prtest.cppalliance.org/genhtml/index.html](https://741.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-04-21 04:28:26 UTC  
> Url: https://github.com/boostorg/url/pull/741#issuecomment-1517240731  

GCOVR code coverage report [https://741.url.prtest.cppalliance.org/gcovr/index.html](https://741.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://741.url.prtest.cppalliance.org/genhtml/index.html](https://741.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-04-21 04:41:59 UTC  
> Url: https://github.com/boostorg/url/pull/741#issuecomment-1517248004  

GCOVR code coverage report [https://741.url.prtest.cppalliance.org/gcovr/index.html](https://741.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://741.url.prtest.cppalliance.org/genhtml/index.html](https://741.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2023-04-21 05:19:33 UTC  
> Url: https://github.com/boostorg/url/pull/741#issuecomment-1517275637  

GCOVR code coverage report [https://741.url.prtest.cppalliance.org/gcovr/index.html](https://741.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://741.url.prtest.cppalliance.org/genhtml/index.html](https://741.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2023-04-21 05:36:48 UTC  
> Url: https://github.com/boostorg/url/pull/741#issuecomment-1517286691  

GCOVR code coverage report [https://741.url.prtest.cppalliance.org/gcovr/index.html](https://741.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://741.url.prtest.cppalliance.org/genhtml/index.html](https://741.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2023-04-21 05:52:28 UTC  
> Url: https://github.com/boostorg/url/pull/741#issuecomment-1517297315  

GCOVR code coverage report [https://741.url.prtest.cppalliance.org/gcovr/index.html](https://741.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://741.url.prtest.cppalliance.org/genhtml/index.html](https://741.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2023-04-21 06:08:26 UTC  
> Url: https://github.com/boostorg/url/pull/741#issuecomment-1517311830  

GCOVR code coverage report [https://741.url.prtest.cppalliance.org/gcovr/index.html](https://741.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://741.url.prtest.cppalliance.org/genhtml/index.html](https://741.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2023-04-21 06:57:57 UTC  
> Url: https://github.com/boostorg/url/pull/741#issuecomment-1517357313  

GCOVR code coverage report [https://741.url.prtest.cppalliance.org/gcovr/index.html](https://741.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://741.url.prtest.cppalliance.org/genhtml/index.html](https://741.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://741.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---
