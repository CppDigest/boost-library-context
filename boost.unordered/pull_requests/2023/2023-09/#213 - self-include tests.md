# #213 self-include tests [Merged]

> Username: cmazakas  
> Created at: 2023-09-18 18:51:23 UTC  
> Updated at: 2023-09-22 20:02:48 UTC  
> Merged at: 2023-09-22 20:02:45 UTC  
> Closed at: 2023-09-22 20:02:45 UTC  
> Url: https://github.com/boostorg/unordered/pull/213  

Finally add a missing piece of the test suite  
  
Ensure all project headers compile with:  
```cpp  
#include <boost/unordered/...>  
int main() {}  
```

---

## Comment 1

> Username: joaquintides  
> Created_at: 2023-09-19 08:52:28 UTC  
> Url: https://github.com/boostorg/unordered/pull/213#issuecomment-1725089132  

Maybe, instead of having as many different test executables as there are headers, we can have multiple obj files linked into one executable? This probably takes less CI time.

---

## Comment 2

> Username: cmazakas  
> Created_at: 2023-09-19 14:51:27 UTC  
> Updated_at: 2023-09-19 15:35:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/213#issuecomment-1725801884  

> Maybe, instead of having as many different test executables as there are headers, we can have multiple obj files linked into one executable? This probably takes less CI time.  
  
Typically I prefer anything that enables me to run just a single test, should I ever need to, but I can look into this.  
  
Edit:  
  
looks like all we'd need to do here is just use b2's builtin `compile` rule.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2023-09-22 10:28:42 UTC  
> Url: https://github.com/boostorg/unordered/pull/213#issuecomment-1731186736  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/213?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#213](https://app.codecov.io/gh/boostorg/unordered/pull/213?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (08187c7) into [develop](https://app.codecov.io/gh/boostorg/unordered/commit/73891c6ec8ee1b2896f7b8a3a29612d9a46daeac?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (73891c6) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/213/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/213?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #213   +/-   ##  
========================================  
  Coverage    98.13%   98.13%             
========================================  
  Files          141      141             
  Lines        19508    19508             
========================================  
  Hits         19145    19145             
  Misses         363      363             
```  
  
  
| [Files Changed](https://app.codecov.io/gh/boostorg/unordered/pull/213?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...lude/boost/unordered/detail/foa/node\_map\_types.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/213?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9ub2RlX21hcF90eXBlcy5ocHA=) | `100.00% <ø> (ø)` | |  
| [...lude/boost/unordered/detail/foa/node\_set\_types.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/213?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9ub2RlX3NldF90eXBlcy5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/213?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/213?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [73891c6...08187c7](https://app.codecov.io/gh/boostorg/unordered/pull/213?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
