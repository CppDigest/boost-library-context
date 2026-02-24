# #61 Get rid of warning about misleading indentation in matrix_expression.hpp [Closed]

> Username: hlindberg  
> Created at: 2018-11-04 17:09:08 UTC  
> Updated at: 2019-04-21 18:58:29 UTC  
> Closed at: 2019-04-21 18:58:29 UTC  
> Url: https://github.com/boostorg/ublas/pull/61  

This adds braces and fixes indentation that results in compilation warnings.  
  
(Not sure if it is correct to target the `develop` branch or if this should go against `master`).

---

## Comment 1

> Username: austin-beer  
> Created_at: 2018-11-06 00:27:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/61#issuecomment-436086862  

This is a duplicate of https://github.com/boostorg/ublas/pull/50, which unfortunately has been open and unmerged for almost a year. I'm encountering this same warning as well and I would like to eliminate it. I wonder who is responsible for merging these pull requests?

---

## Comment 2

> Username: austin-beer  
> Created_at: 2019-04-19 19:18:45 UTC  
> Url: https://github.com/boostorg/ublas/pull/61#issuecomment-484991109  

This issue was fixed in 53361d7f42e7d88661c147a2490e10ee32324e48 and so this PR can be closed.

---
