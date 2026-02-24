# #189 fix build error after #include <windows.h> [Merged]

> Username: wevsty  
> Created at: 2023-08-12 14:16:39 UTC  
> Updated at: 2023-08-13 19:19:02 UTC  
> Merged at: 2023-08-13 19:19:02 UTC  
> Closed at: 2023-08-13 19:19:02 UTC  
> Url: https://github.com/boostorg/locale/pull/189  

The following code under MSVC on Windows gives a compilation error.  
  
```cpp  
#include <windows.h>  
#include <boost/locale.hpp>  
  
int main()  
{  
    return 0;  
}  
```  
```  
error message  
error C2589: '(' : illegal token on right side of '::'  
```  
This problem occurs because windows.h contains a macro definition for min().  
The conflict is caused by the use of the min keyword in include/boost/locale/util/string.hpp.  
  
This PR solves that problem.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-08-12 19:03:26 UTC  
> Updated_at: 2023-08-13 13:50:12 UTC  
> Url: https://github.com/boostorg/locale/pull/189#issuecomment-1676056850  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/189?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#189](https://app.codecov.io/gh/boostorg/locale/pull/189?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (368f769) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/aab426aca0ac2d6851c623cce179547fdeedc542?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (aab426a) will **decrease** coverage by `0.02%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/189/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/189?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #189      +/-   ##  
===========================================  
- Coverage    94.70%   94.69%   -0.02%       
===========================================  
  Files          113      113                
  Lines         9857     9857                
===========================================  
- Hits          9335     9334       -1       
- Misses         522      523       +1       
```  
  
  
| [Files Changed](https://app.codecov.io/gh/boostorg/locale/pull/189?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/util/string.hpp](https://app.codecov.io/gh/boostorg/locale/pull/189?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRpbC9zdHJpbmcuaHBw) | `100.00% <100.00%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/locale/pull/189/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/189?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/189?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [aab426a...368f769](https://app.codecov.io/gh/boostorg/locale/pull/189?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 2 [Commented]

> Username: Flamefire  
> Created_at: 2023-08-13 13:27:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/locale/pull/189#pullrequestreview-1575826103  

Thanks!  
This wasn't caught before as our code defines `NOMINMAX` in various places which avoids those macro definitions

---

## Comment 3

> Username: wevsty  
> Created_at: 2023-08-13 14:30:51 UTC  
> Url: https://github.com/boostorg/locale/pull/189#issuecomment-1676377300  

I didn't notice that the automatic merge was clicked close, and it seemed that the automatic merge was canceled when it was reopened.  
I will close the PR after waiting for the merge.

---

## Comment 4

> Username: Flamefire  
> Created_at: 2023-08-13 15:17:04 UTC  
> Url: https://github.com/boostorg/locale/pull/189#issuecomment-1676389721  

> I didn't notice that the automatic merge was clicked close, and it seemed that the automatic merge was canceled when it was reopened. I will close the PR after waiting for the merge.  
  
There is no need to manually close it. Merging automatically closes it.

---
