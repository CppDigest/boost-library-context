# #636 [strategies] Do not include karney strategy and parameters by default. [Merged]

> Username: awulkiew  
> Created at: 2019-11-05 12:58:16 UTC  
> Updated at: 2019-11-05 15:16:29 UTC  
> Merged at: 2019-11-05 15:16:28 UTC  
> Closed at: 2019-11-05 15:16:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/636  

The reasons are:  
- karney formulas expect degrees so they won't work with strategies right now  
- `karney_inverse` requires C++11 (and doesn't compile with older VS because of min/max macros)  
  
Regarding the second bullet, see: https://www.boost.org/development/tests/develop/developer/geometry.html  
  
If this PR is merged the formulas will be reincluded when https://github.com/boostorg/geometry/issues/635 is addressed and the inverse formula is either adapted to C++03 or the standard required by the library is bumped to C++11.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2019-11-05 15:16:14 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/636#pullrequestreview-311807103  

Ok let's do this for now. Thanks!

---
