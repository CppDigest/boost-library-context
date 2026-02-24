# #647 Fix odr-used constants and improve const-correctness in karney_inverse. [Merged]

> Username: awulkiew  
> Created at: 2019-11-19 23:50:57 UTC  
> Updated at: 2019-11-20 08:49:44 UTC  
> Merged at: 2019-11-20 08:49:44 UTC  
> Closed at: 2019-11-20 08:49:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/647  

Using static const variables instead of static constexpr variables also  
enables the use of user-defined types without constexpr constructor.  
  
My hope is to fix linking errors recently detected at CircleCI [1] after https://github.com/boostorg/geometry/pull/644 was merged, e.g.:  
  
    boost/geometry/formulas/karney_inverse.hpp:182: undefined reference to `boost::geometry::formula::karney_inverse<double, true, true, true, true, true, 8ul>::c90'  
  
[1] https://circleci.com/gh/boostorg/geometry/6716

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2019-11-20 08:49:33 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/647#pullrequestreview-319674144  

Thanks!

---
