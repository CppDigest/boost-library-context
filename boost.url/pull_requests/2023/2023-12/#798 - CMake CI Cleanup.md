# #798 CMake CI Cleanup [Closed]

> Username: cmazakas  
> Created at: 2023-12-05 23:17:20 UTC  
> Updated at: 2023-12-06 23:06:06 UTC  
> Closed at: 2023-12-06 23:06:05 UTC  
> Url: https://github.com/boostorg/url/pull/798  

URL's `find_package()` CMake test doesn't actually invoke any methods defined in the found DLL for Windows platforms with `BUILD_SHARED_LIBS`.  
  
This PR updates the test to use components of URL with out-of-line definitions which now triggers an expected CI failure.  
  
The fix is somewhat straight-forward, just updating he `$GITHUB_PATH` in any step before the Find Package one in the ci.yml.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-12-05 23:39:55 UTC  
> Updated_at: 2023-12-05 23:40:27 UTC  
> Url: https://github.com/boostorg/url/pull/798#issuecomment-1841803700  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/798?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#798](https://app.codecov.io/gh/boostorg/url/pull/798?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (60d94e4) into [develop](https://app.codecov.io/gh/boostorg/url/commit/d99b89ac160fca4ad3a6d8b425db1a4a5ef0638f?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d99b89a) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/798/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/798?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #798   +/-   ##  
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
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/798?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/798?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d99b89a...60d94e4](https://app.codecov.io/gh/boostorg/url/pull/798?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: alandefreitas  
> Created_at: 2023-12-06 23:06:05 UTC  
> Url: https://github.com/boostorg/url/pull/798#issuecomment-1843831068  

Thanks. Merged into #799, which also covers all other forms of integrating the library. There might be some changes you find useful 84b82b88.

---
