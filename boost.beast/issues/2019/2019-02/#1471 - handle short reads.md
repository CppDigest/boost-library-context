# #1471 - handle short reads [Closed]

> Username: vinniefalco  
> Created at: 2019-02-22 03:45:30 UTC  
> Updated at: 2019-02-23 19:39:56 UTC  
> Closed at: 2019-02-23 19:39:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1471  

Short reads are only a problem when the message size is not known. For both HTTP and WebSocket we can safely ignore the short reads in almost all cases in a detectable, safe fashion. This should be implemented.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-23 19:39:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1471#issuecomment-466684272  

Duplicate of #38
