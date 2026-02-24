# #160 Add test for parsing the plural expression in catalogs [Merged]

> Username: Flamefire  
> Created at: 2023-04-10 17:52:27 UTC  
> Updated at: 2023-04-14 13:15:10 UTC  
> Merged at: 2023-04-14 13:14:56 UTC  
> Closed at: 2023-04-14 13:14:56 UTC  
> Url: https://github.com/boostorg/locale/pull/160  

The parser and evaluator of plural expressions in PO/MO files isn't really tested thorouhly.  
Add tests for expressions from some languages as well as some basic tests for all possible operations comparing those against the result of calculating the value in C++.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-04-12 20:07:23 UTC  
> Updated_at: 2023-04-13 22:46:44 UTC  
> Url: https://github.com/boostorg/locale/pull/160#issuecomment-1505856730  

## [Codecov](https://codecov.io/gh/boostorg/locale/pull/160?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#160](https://codecov.io/gh/boostorg/locale/pull/160?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b3fe45e) into [develop](https://codecov.io/gh/boostorg/locale/commit/91783532a580f0f867c80bbd3bc9be5b7372b2dd?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9178353) will **increase** coverage by `0.49%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/160/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/160?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #160      +/-   ##  
===========================================  
+ Coverage    89.52%   90.01%   +0.49%       
===========================================  
  Files          109      110       +1       
  Lines         9534     9635     +101       
===========================================  
+ Hits          8535     8673     +138       
+ Misses         999      962      -37       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/160?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/shared/mo\_lambda.hpp](https://codecov.io/gh/boostorg/locale/pull/160?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvbW9fbGFtYmRhLmhwcA==) | `100.00% <ø> (ø)` | |  
| [src/boost/locale/icu/date\_time.cpp](https://codecov.io/gh/boostorg/locale/pull/160?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZGF0ZV90aW1lLmNwcA==) | `83.07% <100.00%> (ø)` | |  
| [test/test\_catalog.cpp](https://codecov.io/gh/boostorg/locale/pull/160?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NhdGFsb2cuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_date\_time.cpp](https://codecov.io/gh/boostorg/locale/pull/160?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2RhdGVfdGltZS5jcHA=) | `98.30% <100.00%> (+<0.01%)` | :arrow_up: |  
| [test/test\_message.cpp](https://codecov.io/gh/boostorg/locale/pull/160?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X21lc3NhZ2UuY3Bw) | `97.69% <100.00%> (+0.57%)` | :arrow_up: |  
  
... and [1 file with indirect coverage changes](https://codecov.io/gh/boostorg/locale/pull/160/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://codecov.io/gh/boostorg/locale/pull/160?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/160?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9178353...b3fe45e](https://codecov.io/gh/boostorg/locale/pull/160?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
