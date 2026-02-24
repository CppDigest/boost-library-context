# #350 Add a missing include to topology.hpp [Merged]

> Username: vslashg  
> Created at: 2023-09-08 18:11:29 UTC  
> Updated at: 2023-09-12 03:26:56 UTC  
> Merged at: 2023-09-12 03:26:55 UTC  
> Closed at: 2023-09-12 03:26:55 UTC  
> Url: https://github.com/boostorg/graph/pull/350  

boost::shared_ptr is used in this file without the corresponding include, so currently this file does not compile on its own.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2023-09-12 03:26:33 UTC  
> Url: https://github.com/boostorg/graph/pull/350#issuecomment-1714907275  

Thanks for catching and fixing that!

---
