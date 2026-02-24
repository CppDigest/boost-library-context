# #706 detail::to_string_view [Merged]

> Username: alandefreitas  
> Created at: 2023-03-06 14:46:25 UTC  
> Updated at: 2024-02-21 00:55:24 UTC  
> Merged at: 2023-03-07 14:29:52 UTC  
> Closed at: 2023-03-07 14:29:52 UTC  
> Url: https://github.com/boostorg/url/pull/706  

fix #672

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-03-06 15:04:58 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1456304455  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-03-06 15:07:30 UTC  
> Updated_at: 2023-03-07 13:43:20 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1456309261  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/706?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#706](https://codecov.io/gh/boostorg/url/pull/706?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (0e5e025) into [develop](https://codecov.io/gh/boostorg/url/commit/1aa13e157d368f481078552980b36978045f3072?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1aa13e1) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 0e5e025 differs from pull request most recent head 029c46e. Consider uploading reports for the commit 029c46e to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/706/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/706?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #706   +/-   ##  
========================================  
  Coverage    97.13%   97.13%             
========================================  
  Files          154      155    +1       
  Lines         8440     8446    +6       
========================================  
+ Hits          8198     8204    +6       
  Misses         242      242             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/706?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/decode\_view.hpp](https://codecov.io/gh/boostorg/url/pull/706?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGVjb2RlX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/any\_segments\_iter.hpp](https://codecov.io/gh/boostorg/url/pull/706?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2FueV9zZWdtZW50c19pdGVyLmhwcA==) | `98.55% <100.00%> (ø)` | |  
| [include/boost/url/detail/impl/pattern.ipp](https://codecov.io/gh/boostorg/url/pull/706?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvcGF0dGVybi5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/detail/optional\_string.hpp](https://codecov.io/gh/boostorg/url/pull/706?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL29wdGlvbmFsX3N0cmluZy5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/detail/string\_view.hpp](https://codecov.io/gh/boostorg/url/pull/706?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3N0cmluZ192aWV3LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [...nclude/boost/url/grammar/impl/string\_view\_base.ipp](https://codecov.io/gh/boostorg/url/pull/706?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9pbXBsL3N0cmluZ192aWV3X2Jhc2UuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/grammar/string\_view\_base.hpp](https://codecov.io/gh/boostorg/url/pull/706?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9zdHJpbmdfdmlld19iYXNlLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/pct\_string\_view.hpp](https://codecov.io/gh/boostorg/url/pull/706?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGN0X3N0cmluZ192aWV3LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/url\_view.hpp](https://codecov.io/gh/boostorg/url/pull/706?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXcuaHBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/706?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/706?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1aa13e1...029c46e](https://codecov.io/gh/boostorg/url/pull/706?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-03-06 15:36:11 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1456361051  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-03-06 16:05:14 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1456414801  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-03-06 16:43:46 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1456493789  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-03-06 16:53:44 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1456509822  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-03-06 18:00:13 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1456657861  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-03-06 18:11:02 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1456684771  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-03-06 18:31:06 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1456728173  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-03-06 18:40:58 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1456744389  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2023-03-06 18:50:59 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1456760525  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2023-03-06 19:01:06 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1456777729  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2023-03-06 19:11:07 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1456796869  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2023-03-06 19:40:01 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1456845428  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2023-03-06 20:14:27 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1456905798  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2023-03-06 20:54:42 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1456977566  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2023-03-06 21:44:48 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1457047261  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2023-03-06 22:01:52 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1457074794  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2023-03-06 22:16:13 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1457094097  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 20

> Username: alandefreitas  
> Created_at: 2023-03-06 22:39:25 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1457136856  

@swex Would you like to check if this PR fixes #672 for you?

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2023-03-06 22:40:18 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1457137918  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2023-03-07 04:34:38 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1457514642  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2023-03-07 14:01:09 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1458225756  

GCOVR code coverage report [https://706.url.prtest.cppalliance.org/gcovr/index.html](https://706.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://706.url.prtest.cppalliance.org/genhtml/index.html](https://706.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://706.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 24

> Username: swex  
> Created_at: 2023-03-08 00:27:08 UTC  
> Url: https://github.com/boostorg/url/pull/706#issuecomment-1459078846  

> @swex Would you like to check if this PR fixes #672 for you?  
  
yes, it works on my setup now, thank you.

---
