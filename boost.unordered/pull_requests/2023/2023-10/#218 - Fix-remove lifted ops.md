# #218 Fix/remove lifted ops [Merged]

> Username: joaquintides  
> Created at: 2023-10-21 17:16:55 UTC  
> Updated at: 2023-10-22 18:16:09 UTC  
> Merged at: 2023-10-22 18:16:06 UTC  
> Closed at: 2023-10-22 18:16:06 UTC  
> Url: https://github.com/boostorg/unordered/pull/218  

Opening the PR for discussion. I've removed all lifting `using` directives for `swap` and `operator(==|!=)`, _except_ in the case of `boost::unordered_[multi](map|set)`, and I wonder if we should go ahead and remove those too --I can't think of any legitimate use case where they could be needed.

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-10-21 18:06:54 UTC  
> Url: https://github.com/boostorg/unordered/pull/218#issuecomment-1773881177  

We should remove the 'lifting' for `operator==` and `operator!=` because only test suites do `boost::operator==(x, y)` instead of the correct `x == y`. For `swap`, things are less clear because we had a function `boost::swap` in Core so it's quite possible for code to have done `boost::swap(x, y)` for `unordered_set` or `unordered_map`.  
  
If we remove the using directive, this will probably stop compiling, or start doing who knows what.

---

## Comment 2

> Username: joaquintides  
> Created_at: 2023-10-21 18:35:05 UTC  
> Updated_at: 2023-10-21 18:36:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/218#issuecomment-1773888748  

> If we remove the using directive, this will probably stop compiling, or start doing who knows what.  
  
From what I can gather from [boost::swap reference](https://www.boost.org/doc/libs/1_83_0/libs/core/doc/html/core/swap.html), ADL should kick in, so it seems safe to me to drop the lift. I've tested the following locally (on the `fix/remove_lifted_ops` branch):  
  
```cpp  
#include <boost/unordered/unordered_flat_map.hpp>  
#include <boost/core/swap.hpp>  
  
int main()  
{  
#pragma warning(disable : 4996)  
  
  boost::unordered_flat_map<int,int> fm1,fm2;  
  boost::swap(fm1,fm2);  
}  
```  
  
and everything works fine. (BTW, the docs for `boost::swap` do not indicate that the function is deprecated.)

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-10-21 18:42:41 UTC  
> Url: https://github.com/boostorg/unordered/pull/218#issuecomment-1773891133  

It won't work if boost/core/swap.hpp isn't included, though.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2023-10-22 03:26:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/218#issuecomment-1773982635  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/218?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#218](https://app.codecov.io/gh/boostorg/unordered/pull/218?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f4667f5) into [develop](https://app.codecov.io/gh/boostorg/unordered/commit/ea4fc5e66d1dfb658e16dcefc1159fc5d7c56be5?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (ea4fc5e) will **increase** coverage by `0.01%`.  
> Report is 1 commits behind head on develop.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/218/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/218?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #218      +/-   ##  
===========================================  
+ Coverage    98.07%   98.08%   +0.01%       
===========================================  
  Files          143      143                
  Lines        19693    19798     +105       
===========================================  
+ Hits         19314    19419     +105       
  Misses         379      379                
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/unordered/pull/218?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/concurrent\_flat\_map.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/218?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvY29uY3VycmVudF9mbGF0X21hcC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/unordered/concurrent\_flat\_set.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/218?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvY29uY3VycmVudF9mbGF0X3NldC5ocHA=) | `100.00% <ø> (ø)` | |  
| [test/unordered/compile\_tests.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/218?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvY29tcGlsZV90ZXN0cy5ocHA=) | `100.00% <ø> (ø)` | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/unordered/pull/218/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/218?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/218?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ea4fc5e...f4667f5](https://app.codecov.io/gh/boostorg/unordered/pull/218?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
