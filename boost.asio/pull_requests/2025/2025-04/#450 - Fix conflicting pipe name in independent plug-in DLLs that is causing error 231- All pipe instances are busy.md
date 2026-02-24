# #450 Fix conflicting pipe name in independent plug-in DLLs that is causing error 231: All pipe instances are busy [Open]

> Username: RK-BFX  
> Created at: 2025-04-10 18:58:35 UTC  
> Updated at: 2025-04-10 18:58:35 UTC  
> Url: https://github.com/boostorg/asio/pull/450  

Include numerical representation of local static variable's address into the pipe name to discriminate Boost.Asio instances in independent DLLs.  
  
Fixes https://github.com/chriskohlhoff/asio/issues/1619  
  
Also make the code slightly more future-proof and add useful comments and external documentation links.

---
