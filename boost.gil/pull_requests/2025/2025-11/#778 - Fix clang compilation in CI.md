# #778 Fix clang compilation in CI [Merged]

> Username: sdebionne  
> Created at: 2025-11-23 16:57:14 UTC  
> Updated at: 2025-11-26 12:02:44 UTC  
> Merged at: 2025-11-26 11:51:14 UTC  
> Closed at: 2025-11-26 11:51:14 UTC  
> Url: https://github.com/boostorg/gil/pull/778  

## Description  
  
Two CI jobs are currently failing. Investigate if they can be fixed or if they are deprecated and can be removed.  
  
### References  
  
[posix (clang, clang++-5.0, 14,1z, ubuntu-latest, ubuntu:18.04, clang-5.0)](https://github.com/boostorg/gil/actions/runs/17262406016/job/48986725091#logs)  
  
[posix (clang, 14,17, macos-13)](https://github.com/boostorg/gil/actions/runs/17262406016/job/48986725166#logs)  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-11-23 17:02:52 UTC  
> Updated_at: 2025-11-26 11:51:32 UTC  
> Url: https://github.com/boostorg/gil/pull/778#issuecomment-3568155489  

## [Codecov](https://app.codecov.io/gh/boostorg/gil/pull/778?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 81.99%. Comparing base ([`06c421a`](https://app.codecov.io/gh/boostorg/gil/commit/06c421ae74062bfa112dc3bacaef7746a85467d2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`82fb089`](https://app.codecov.io/gh/boostorg/gil/commit/82fb0899dd0145efe61b15838439034ef4c26400?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 25 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #778      +/-   ##  
===========================================  
- Coverage    82.34%   81.99%   -0.35%       
===========================================  
  Files          117      117                
  Lines         5726     5354     -372       
===========================================  
- Hits          4715     4390     -325       
+ Misses        1011      964      -47       
```  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: sdebionne  
> Created_at: 2025-11-24 17:25:13 UTC  
> Url: https://github.com/boostorg/gil/pull/778#issuecomment-3571922813  

**On (clang, 14,17, macos-13)**  
   
The build fails with `cxxstd=17`:  
  
```  
dyld[33139]: Symbol not found: __ZNSt3__13pmr20get_default_resourceEv  
  Referenced from: <426B1D7D-5F1D-3549-BDD0-112914243EED> /Users/runner/work/gil/boost-root/bin.v2/libs/gil/test/core/image/image.test/clang-darwin-15/debug/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden/image  
  Expected in:     <8CAC377D-FCC3-34DD-8FA6-C76EBB4D68C9> /usr/lib/libc++.1.dylib  
```  
  
**On (clang, clang++-5.0, 14,1z, ubuntu-latest, ubuntu:18.04, clang-5.0)**   
  
The build is completely broken with `cxxstd=1z`

---

## Comment 3

> Username: sdebionne  
> Created_at: 2025-11-24 17:50:36 UTC  
> Url: https://github.com/boostorg/gil/pull/778#issuecomment-3572024097  

The first issue is a well know issue of the libc++ that ships with macos 13 and can eventually be fixed with a polyfill like https://github.com/acts-project/vecmem/pull/105/files  
  
As for the second issue, I am in favor of dropping this variant.

---

## Comment 4

> Username: mloskot  
> Created_at: 2025-11-24 18:17:16 UTC  
> Url: https://github.com/boostorg/gil/pull/778#issuecomment-3572116568  

I also think dropping that variant sounds sensible.

---

## Comment 5

> Username: sdebionne  
> Created_at: 2025-11-25 20:25:49 UTC  
> Url: https://github.com/boostorg/gil/pull/778#issuecomment-3577452972  

Regarding macos 13, I have just got this  
  
This is a scheduled macos-13 brownout. The macOS-13 based runner images are being deprecated. For more details, see https://github.com/actions/runner-images/issues/13046.  
  
So the effort to fix it is not worth it.

---

## Comment 6

> Username: mloskot  
> Created_at: 2025-11-26 12:02:44 UTC  
> Url: https://github.com/boostorg/gil/pull/778#issuecomment-3580998432  

Awesome, thank you!

---
