# #502 Fix missing header in numeric/kernel.hpp to make it self-contained. [Merged]

> Username: sdebionne  
> Created at: 2020-06-23 13:35:51 UTC  
> Updated at: 2020-10-14 08:11:22 UTC  
> Merged at: 2020-06-25 08:22:52 UTC  
> Closed at: 2020-06-25 08:22:52 UTC  
> Url: https://github.com/boostorg/gil/pull/502  

`#include <stdexcept>` is missing in `numeric/kernel.hpp`.  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2020-06-25 08:22:32 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/502#pullrequestreview-437272751  

Thank you very much @sdebionne

---
