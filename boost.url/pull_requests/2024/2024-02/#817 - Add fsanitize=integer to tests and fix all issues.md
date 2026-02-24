# #817 Add fsanitize=integer to tests and fix all issues [Merged]

> Username: alandefreitas  
> Created at: 2024-02-05 20:47:22 UTC  
> Updated at: 2024-02-21 00:56:04 UTC  
> Merged at: 2024-02-07 21:02:58 UTC  
> Closed at: 2024-02-07 21:02:58 UTC  
> Url: https://github.com/boostorg/url/pull/817  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-02-05 21:11:35 UTC  
> Url: https://github.com/boostorg/url/pull/817#issuecomment-1928100960  

GCOVR code coverage report [https://817.url.prtest2.cppalliance.org/gcovr/index.html](https://817.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://817.url.prtest2.cppalliance.org/genhtml/index.html](https://817.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-02-06 00:18:58 UTC  
> Updated_at: 2024-02-07 20:15:11 UTC  
> Url: https://github.com/boostorg/url/pull/817#issuecomment-1928550304  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/817?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`8a82041`)](https://app.codecov.io/gh/boostorg/url/commit/8a8204103f21b5fa0c05d33556f3395913f0e9c2?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 97.24% compared to head [(`0d60e4d`)](https://app.codecov.io/gh/boostorg/url/pull/817?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 97.24%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/817/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/817?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #817   +/-   ##  
========================================  
  Coverage    97.24%   97.24%             
========================================  
  Files          157      157             
  Lines         8587     8595    +8       
========================================  
+ Hits          8350     8358    +8       
  Misses         237      237             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/url/pull/817?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/detail/encode.hpp](https://app.codecov.io/gh/boostorg/url/pull/817?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2VuY29kZS5ocHA=) | `64.10% <100.00%> (+0.46%)` | :arrow_up: |  
| [include/boost/url/detail/url\_impl.hpp](https://app.codecov.io/gh/boostorg/url/pull/817?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3VybF9pbXBsLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/grammar/lut\_chars.hpp](https://app.codecov.io/gh/boostorg/url/pull/817?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9sdXRfY2hhcnMuaHBw) | `100.00% <100.00%> (ø)` | |  
| [src/detail/url\_impl.cpp](https://app.codecov.io/gh/boostorg/url/pull/817?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC91cmxfaW1wbC5jcHA=) | `97.68% <100.00%> (+0.05%)` | :arrow_up: |  
| [src/url\_base.cpp](https://app.codecov.io/gh/boostorg/url/pull/817?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3VybF9iYXNlLmNwcA==) | `99.63% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/817?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/817?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [8a82041...0d60e4d](https://app.codecov.io/gh/boostorg/url/pull/817?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-02-06 00:21:50 UTC  
> Url: https://github.com/boostorg/url/pull/817#issuecomment-1928553131  

GCOVR code coverage report [https://817.url.prtest2.cppalliance.org/gcovr/index.html](https://817.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://817.url.prtest2.cppalliance.org/genhtml/index.html](https://817.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-02-06 01:36:40 UTC  
> Url: https://github.com/boostorg/url/pull/817#issuecomment-1928616341  

GCOVR code coverage report [https://817.url.prtest2.cppalliance.org/gcovr/index.html](https://817.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://817.url.prtest2.cppalliance.org/genhtml/index.html](https://817.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-02-06 16:37:41 UTC  
> Url: https://github.com/boostorg/url/pull/817#issuecomment-1930288283  

GCOVR code coverage report [https://817.url.prtest2.cppalliance.org/gcovr/index.html](https://817.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://817.url.prtest2.cppalliance.org/genhtml/index.html](https://817.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2024-02-06 18:36:30 UTC  
> Url: https://github.com/boostorg/url/pull/817#issuecomment-1930537780  

GCOVR code coverage report [https://817.url.prtest2.cppalliance.org/gcovr/index.html](https://817.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://817.url.prtest2.cppalliance.org/genhtml/index.html](https://817.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2024-02-06 19:09:54 UTC  
> Url: https://github.com/boostorg/url/pull/817#issuecomment-1930587245  

GCOVR code coverage report [https://817.url.prtest2.cppalliance.org/gcovr/index.html](https://817.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://817.url.prtest2.cppalliance.org/genhtml/index.html](https://817.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2024-02-06 20:59:39 UTC  
> Url: https://github.com/boostorg/url/pull/817#issuecomment-1930741575  

GCOVR code coverage report [https://817.url.prtest2.cppalliance.org/gcovr/index.html](https://817.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://817.url.prtest2.cppalliance.org/genhtml/index.html](https://817.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2024-02-06 21:17:07 UTC  
> Url: https://github.com/boostorg/url/pull/817#issuecomment-1930765222  

GCOVR code coverage report [https://817.url.prtest2.cppalliance.org/gcovr/index.html](https://817.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://817.url.prtest2.cppalliance.org/genhtml/index.html](https://817.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2024-02-06 22:47:30 UTC  
> Url: https://github.com/boostorg/url/pull/817#issuecomment-1930898851  

GCOVR code coverage report [https://817.url.prtest2.cppalliance.org/gcovr/index.html](https://817.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://817.url.prtest2.cppalliance.org/genhtml/index.html](https://817.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2024-02-06 23:54:45 UTC  
> Url: https://github.com/boostorg/url/pull/817#issuecomment-1930969335  

GCOVR code coverage report [https://817.url.prtest2.cppalliance.org/gcovr/index.html](https://817.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://817.url.prtest2.cppalliance.org/genhtml/index.html](https://817.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2024-02-07 14:50:42 UTC  
> Url: https://github.com/boostorg/url/pull/817#issuecomment-1932209863  

GCOVR code coverage report [https://817.url.prtest2.cppalliance.org/gcovr/index.html](https://817.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://817.url.prtest2.cppalliance.org/genhtml/index.html](https://817.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2024-02-07 15:07:00 UTC  
> Url: https://github.com/boostorg/url/pull/817#issuecomment-1932243991  

GCOVR code coverage report [https://817.url.prtest2.cppalliance.org/gcovr/index.html](https://817.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://817.url.prtest2.cppalliance.org/genhtml/index.html](https://817.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2024-02-07 16:37:21 UTC  
> Url: https://github.com/boostorg/url/pull/817#issuecomment-1932437755  

GCOVR code coverage report [https://817.url.prtest2.cppalliance.org/gcovr/index.html](https://817.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://817.url.prtest2.cppalliance.org/genhtml/index.html](https://817.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2024-02-07 17:15:59 UTC  
> Url: https://github.com/boostorg/url/pull/817#issuecomment-1932516492  

GCOVR code coverage report [https://817.url.prtest2.cppalliance.org/gcovr/index.html](https://817.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://817.url.prtest2.cppalliance.org/genhtml/index.html](https://817.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2024-02-07 17:46:49 UTC  
> Url: https://github.com/boostorg/url/pull/817#issuecomment-1932568078  

GCOVR code coverage report [https://817.url.prtest2.cppalliance.org/gcovr/index.html](https://817.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://817.url.prtest2.cppalliance.org/genhtml/index.html](https://817.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2024-02-07 18:26:15 UTC  
> Url: https://github.com/boostorg/url/pull/817#issuecomment-1932631012  

GCOVR code coverage report [https://817.url.prtest2.cppalliance.org/gcovr/index.html](https://817.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://817.url.prtest2.cppalliance.org/genhtml/index.html](https://817.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2024-02-07 19:18:05 UTC  
> Url: https://github.com/boostorg/url/pull/817#issuecomment-1932710213  

GCOVR code coverage report [https://817.url.prtest2.cppalliance.org/gcovr/index.html](https://817.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://817.url.prtest2.cppalliance.org/genhtml/index.html](https://817.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2024-02-07 20:08:03 UTC  
> Url: https://github.com/boostorg/url/pull/817#issuecomment-1932783683  

GCOVR code coverage report [https://817.url.prtest2.cppalliance.org/gcovr/index.html](https://817.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://817.url.prtest2.cppalliance.org/genhtml/index.html](https://817.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://817.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---
