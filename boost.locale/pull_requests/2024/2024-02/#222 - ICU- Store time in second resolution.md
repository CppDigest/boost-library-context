# #222 ICU: Store time in second resolution [Merged]

> Username: Flamefire  
> Created at: 2024-02-20 10:08:05 UTC  
> Updated at: 2024-02-21 07:38:04 UTC  
> Merged at: 2024-02-21 07:38:01 UTC  
> Closed at: 2024-02-21 07:38:01 UTC  
> Url: https://github.com/boostorg/locale/pull/222  

ICU was the only backend storing subseconds in its calendar and hence reported fractional times in `date_time::time()`  
However outside of `date_time::time(floor(time))` there was no way setting those milli/nanoseconds.  
This leads to unexpected behavior for comparisons of seemingly identical time points, especially as it depends on the backend.  
  
Closes https://github.com/boostorg/locale/issues/221

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-02-20 21:26:47 UTC  
> Url: https://github.com/boostorg/locale/pull/222#issuecomment-1955129272  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/222?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`741bf1d`)](https://app.codecov.io/gh/boostorg/locale/commit/741bf1d4a9c6dc3a89cdb27b749b57ed7c81722b?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 95.72% compared to head [(`1d03d20`)](https://app.codecov.io/gh/boostorg/locale/pull/222?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 95.70%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/222/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/222?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #222      +/-   ##  
===========================================  
- Coverage    95.72%   95.70%   -0.02%       
===========================================  
  Files          116      116                
  Lines         9937     9946       +9       
===========================================  
+ Hits          9512     9519       +7       
- Misses         425      427       +2       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/locale/pull/222?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/icu/date\_time.cpp](https://app.codecov.io/gh/boostorg/locale/pull/222?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZGF0ZV90aW1lLmNwcA==) | `92.56% <100.00%> (+0.12%)` | :arrow_up: |  
| [test/test\_date\_time.cpp](https://app.codecov.io/gh/boostorg/locale/pull/222?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2RhdGVfdGltZS5jcHA=) | `100.00% <100.00%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/locale/pull/222/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/222?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/222?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [741bf1d...1d03d20](https://app.codecov.io/gh/boostorg/locale/pull/222?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
