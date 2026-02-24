# #163 Enhance plural expression parsing for translations [Merged]

> Username: Flamefire  
> Created at: 2023-04-21 16:44:14 UTC  
> Updated at: 2023-04-24 19:47:02 UTC  
> Merged at: 2023-04-24 19:46:58 UTC  
> Closed at: 2023-04-24 19:46:58 UTC  
> Url: https://github.com/boostorg/locale/pull/163  

- Use `long long` as the count argument for translations  
- Remove bitwise/shift ops  
- Enhanced tests  
- Refactoring for readability  
  
Improved from and hence closes #155

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-04-21 21:09:11 UTC  
> Updated_at: 2023-04-23 15:52:53 UTC  
> Url: https://github.com/boostorg/locale/pull/163#issuecomment-1518338017  

## [Codecov](https://codecov.io/gh/boostorg/locale/pull/163?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#163](https://codecov.io/gh/boostorg/locale/pull/163?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (66c95ab) into [develop](https://codecov.io/gh/boostorg/locale/commit/32f85585ecd41bc62407a0b44da3e4c0a80ecc16?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (32f8558) will **increase** coverage by `0.73%`.  
> The diff coverage is `99.51%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/163/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/163?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #163      +/-   ##  
===========================================  
+ Coverage    89.96%   90.69%   +0.73%       
===========================================  
  Files          111      111                
  Lines         9592     9654      +62       
===========================================  
+ Hits          8629     8756     +127       
+ Misses         963      898      -65       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/163?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/shared/message.cpp](https://codecov.io/gh/boostorg/locale/pull/163?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvbWVzc2FnZS5jcHA=) | `96.64% <97.53%> (+2.46%)` | :arrow_up: |  
| [include/boost/locale/message.hpp](https://codecov.io/gh/boostorg/locale/pull/163?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvbWVzc2FnZS5ocHA=) | `100.00% <100.00%> (+7.28%)` | :arrow_up: |  
| [src/boost/locale/shared/mo\_lambda.cpp](https://codecov.io/gh/boostorg/locale/pull/163?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvbW9fbGFtYmRhLmNwcA==) | `100.00% <100.00%> (+20.21%)` | :arrow_up: |  
| [src/boost/locale/shared/mo\_lambda.hpp](https://codecov.io/gh/boostorg/locale/pull/163?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvbW9fbGFtYmRhLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_catalog.cpp](https://codecov.io/gh/boostorg/locale/pull/163?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NhdGFsb2cuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_message.cpp](https://codecov.io/gh/boostorg/locale/pull/163?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X21lc3NhZ2UuY3Bw) | `100.00% <100.00%> (+2.30%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://codecov.io/gh/boostorg/locale/pull/163?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/163?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [32f8558...66c95ab](https://codecov.io/gh/boostorg/locale/pull/163?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
