# #738 Ada tests [Merged]

> Username: alandefreitas  
> Created at: 2023-04-12 01:40:42 UTC  
> Updated at: 2024-02-21 00:54:21 UTC  
> Merged at: 2023-04-12 19:35:27 UTC  
> Closed at: 2023-04-12 19:35:27 UTC  
> Url: https://github.com/boostorg/url/pull/738  

This pull request (PR) expands upon the work done in https://github.com/boostorg/url/pull/714 by replicating all other additional tests from Ada. While the previous PR focused on replicating basic tests, this one covers all other tests that don't rely on URLs with Unicode which Boost.URL doesn't support.  
  
As with any testing, these tests explore various corner cases, and the expected results may differ between Ada and Boost.URL. In cases where the expected results differ, the expected result in Ada is described and the expected result in Boost.URL is explicitly tested.  
  
Through these tests, a number of bugs were uncovered and fixed in separate commits. Specifically, several url transformations were allowing the path to become the authority, thereby breaking the URL invariants. Additionally, multiple empty path segments were causing unnecessary dot segments to be included in the URL.  
  
Another issue identified during testing was that Boost.URL wasn't maintaining the original user input when an IP address was provided to `set_encoded_host`. Instead, the internal parser converts the user input to the IP binary representation and then converts that to a new string. While not technically a bug, this behavior differs slightly from other setters.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-04-12 02:01:42 UTC  
> Url: https://github.com/boostorg/url/pull/738#issuecomment-1504425613  

GCOVR code coverage report [https://738.url.prtest.cppalliance.org/gcovr/index.html](https://738.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://738.url.prtest.cppalliance.org/genhtml/index.html](https://738.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://738.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://738.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-04-12 14:20:03 UTC  
> Url: https://github.com/boostorg/url/pull/738#issuecomment-1505365577  

GCOVR code coverage report [https://738.url.prtest.cppalliance.org/gcovr/index.html](https://738.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://738.url.prtest.cppalliance.org/genhtml/index.html](https://738.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://738.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://738.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-04-12 15:24:28 UTC  
> Url: https://github.com/boostorg/url/pull/738#issuecomment-1505470288  

GCOVR code coverage report [https://738.url.prtest.cppalliance.org/gcovr/index.html](https://738.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://738.url.prtest.cppalliance.org/genhtml/index.html](https://738.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://738.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://738.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2023-04-12 15:24:51 UTC  
> Updated_at: 2023-04-12 15:29:45 UTC  
> Url: https://github.com/boostorg/url/pull/738#issuecomment-1505470920  

## [Codecov](https://codecov.io/gh/boostorg/url/pull/738?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#738](https://codecov.io/gh/boostorg/url/pull/738?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d769abe) into [develop](https://codecov.io/gh/boostorg/url/commit/eb052deca11195a6bacb60aae7ca08592452f18f?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (eb052de) will **increase** coverage by `0.01%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/738/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/738?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #738      +/-   ##  
===========================================  
+ Coverage    97.21%   97.22%   +0.01%       
===========================================  
  Files          155      155                
  Lines         8486     8517      +31       
===========================================  
+ Hits          8250     8281      +31       
  Misses         236      236                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/738?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/boostorg/url/pull/738?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `99.63% <100.00%> (+<0.01%)` | :arrow_up: |  
  
... and [1 file with indirect coverage changes](https://codecov.io/gh/boostorg/url/pull/738/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://codecov.io/gh/boostorg/url/pull/738?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/738?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [eb052de...d769abe](https://codecov.io/gh/boostorg/url/pull/738?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
