# #212 Rewrite the unsafe_declval function to work well with clang in C++26 … [Closed]

> Username: apolukhin  
> Created at: 2025-06-20 11:12:57 UTC  
> Updated at: 2025-10-28 16:39:45 UTC  
> Closed at: 2025-09-14 11:59:26 UTC  
> Url: https://github.com/boostorg/pfr/pull/212  

…mode and that is able to return prvalues  
  
Looks like `static_cast<T>(*ptr)` to prvalue now fails on clang in C++26. Changed it to function pointer call  
  
Fixes: https://github.com/boostorg/pfr/issues/208  
Probably fixes: https://github.com/boostorg/pfr/issues/173, https://github.com/boostorg/pfr/issues/110, https://github.com/boostorg/pfr/issues/126

---

## Comment 1

> Username: coveralls  
> Created_at: 2025-06-20 12:38:17 UTC  
> Updated_at: 2025-06-20 16:46:11 UTC  
> Url: https://github.com/boostorg/pfr/pull/212#issuecomment-2991425807  

## Pull Request Test Coverage Report for [Build 15783550915](https://coveralls.io/builds/74257901)  
  
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
  
  
  
|  Totals | [![Coverage Status](https://coveralls.io/builds/74257901/badge)](https://coveralls.io/builds/74257901) |  
| :-- | --: |  
| Change from base [Build 15761583817](https://coveralls.io/builds/74240223): |  0.0% |  
| Covered Lines: | 405 |  
| Relevant Lines: | 405 |  
  
---  
##### 💛  - [Coveralls](https://coveralls.io)

---
