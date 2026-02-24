# #675 chore: do not use deprecated header in test or elsewhere [Merged]

> Username: striezel  
> Created at: 2022-05-18 13:49:27 UTC  
> Updated at: 2022-05-18 21:41:03 UTC  
> Merged at: 2022-05-18 21:25:36 UTC  
> Closed at: 2022-05-18 21:25:37 UTC  
> Url: https://github.com/boostorg/gil/pull/675  

### Description  
  
Replaces the use of the deprecated header `<boost/gil/extension/numeric/channel_numeric_operations.hpp>` by the newer header `<boost/gil/channel_numeric_operations.hpp>`. (Move occurred as part of #573.)  
  
This PR also fixes other two instances related to the move + deprecation of headers from the numeric extension.  
  
### References  
  
Using the deprecated header causes a warning like  
```  
../boost/gil/extension/numeric/channel_numeric_operations.hpp:14:1: warning: This header is deprecated. Use <boost/gil/channel_numeric_operations.hpp> instead. [-W#pragma-messages]  
1 warning generated.  
```  
Warning occurs when running tests, e. g. here: https://web.archive.org/web/20220518134058/https://www.boost.org/development/tests/develop/output/teeks99-dkr-dc12-20-gil-clang-linux-12~c++20-warnings.html#channel_numeric_operations  
(archived from [the original](https://www.boost.org/development/tests/develop/output/teeks99-dkr-dc12-20-gil-clang-linux-12~c++20-warnings.html#channel_numeric_operations), because that changes with every new build on the develop branch)  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2022-05-18 21:25:14 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/675#pullrequestreview-977668766  

LGTM. Thanks!

---
