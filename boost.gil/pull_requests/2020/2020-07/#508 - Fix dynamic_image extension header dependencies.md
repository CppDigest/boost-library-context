# #508 Fix dynamic_image extension header dependencies [Merged]

> Username: sdebionne  
> Created at: 2020-07-15 12:52:56 UTC  
> Updated at: 2020-07-27 10:39:26 UTC  
> Merged at: 2020-07-26 19:06:00 UTC  
> Closed at: 2020-07-26 19:06:00 UTC  
> Url: https://github.com/boostorg/gil/pull/508  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Remove `#include <boost/gil.hpp>` from `<boost/gil/extension/dynamic_image/dynamic_image_all.hpp>`. It ends up that all individual headers in the extension were already including what they need.  
  
### References  
  
Fixes #501   
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2020-07-26 18:36:25 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/508#pullrequestreview-455386141  

Thank you!

---
