# #3 Fix reduce for predecessor. [Closed]

> Username: marcinz  
> Created at: 2014-06-20 20:20:55 UTC  
> Updated at: 2014-07-13 03:07:43 UTC  
> Closed at: 2014-07-13 02:54:57 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/3  

The assumption was that the predecessor, which is a distributed vertex descriptor, should be  
the same as the key. The key is actually a local vertex descriptor, and these two types may  
not match.  
  
Signed-off-by: Marcin Zalewski marcin.zalewski@gmail.com

---

## Comment 1

> Username: Belcourt  
> Created_at: 2014-07-12 21:43:59 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/3#issuecomment-48824893  

Please close this PR and submit against develop, if you would.  Once it's tested on develop, it can be merged to master.

---

## Comment 2

> Username: marcinz  
> Created_at: 2014-07-13 03:07:43 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/3#issuecomment-48830406  

Done.

---
