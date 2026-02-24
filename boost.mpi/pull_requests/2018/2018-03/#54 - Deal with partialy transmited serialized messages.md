# #54 Deal with partialy transmited serialized messages. [Merged]

> Username: aminiussi  
> Created at: 2018-03-13 10:05:23 UTC  
> Updated at: 2018-05-20 09:33:23 UTC  
> Merged at: 2018-03-13 14:17:17 UTC  
> Closed at: 2018-03-13 14:17:17 UTC  
> Url: https://github.com/boostorg/mpi/pull/54  

This is a fix proposed by @hirschsn, with some more comments.  
Also, I am going to add a regressions test before merging.  
refs #53

---

## Comment 1

> Username: aminiussi  
> Created_at: 2018-05-20 09:33:23 UTC  
> Url: https://github.com/boostorg/mpi/pull/54#issuecomment-390468905  

The tests are failing at compile time on some non c++11 platform. Since the failure are due to the test code only and can be easily worked around, I'm going to downgrade it.

---
