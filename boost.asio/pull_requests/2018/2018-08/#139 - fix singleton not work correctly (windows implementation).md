# #139 fix singleton not work correctly (windows implementation) [Closed]

> Username: stalkyr  
> Created at: 2018-08-30 13:14:53 UTC  
> Updated at: 2018-11-14 06:14:54 UTC  
> Closed at: 2018-11-14 06:14:54 UTC  
> Url: https://github.com/boostorg/asio/pull/139  

The original code create new instance for each different calling  
threads, instead of just one.  
  
This singleton used by system_executor, thus everytime calling  
 post(system_executor(), [] () { /* worker function /* });  
from new thread create additional cpu_corex2 threads, then eating  
up resources quickly.

---
