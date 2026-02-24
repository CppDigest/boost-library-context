# #452 Fixes possible DoS if a file description has its status changed. [Open]

> Username: hnsdeveloper  
> Created at: 2025-06-04 14:41:28 UTC  
> Updated at: 2025-06-04 14:41:28 UTC  
> Url: https://github.com/boostorg/asio/pull/452  

If a file description has its status flags changed through fcntl, either by another thread/process, a possible DoS might arise, as either recv or send will block. This is an attempt to fix that, by using the MSG_DONTWAIT flag on calls to recv and send.    
  
These are the spots where, to the best of my understanding, the operation should be non blocking.

---
