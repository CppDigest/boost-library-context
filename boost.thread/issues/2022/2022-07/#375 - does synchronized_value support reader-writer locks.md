# #375 - does synchronized_value support reader/writer locks? [Open]

> Username: Jacob-Burckhardt  
> Created at: 2022-07-15 21:06:19 UTC  
> Updated at: 2025-10-03 21:51:38 UTC  
> Url: https://github.com/boostorg/thread/issues/375  

Does synchronized_value support [Readers–writer lock](https://en.wikipedia.org/wiki/Readers%E2%80%93writer_lock)?  I could not find an interface to specify whether access to the lock-protected data will be read-only or whether it will write.

---

## Comment 1

> Username: themightyoarfish  
> Created at: 2024-08-21 13:21:08 UTC  
> Url: https://github.com/boostorg/thread/issues/375#issuecomment-2302046379  

I have the same question.

---

## Comment 2

> Username: mguludag  
> Created at: 2025-10-03 21:51:38 UTC  
> Url: https://github.com/boostorg/thread/issues/375#issuecomment-3367315589  

I checked the implementation of this, and it doesn't support lock_shared, only uses unique_lock. So, adding support for reader-writer lock in this implementation looks possible (maybe introducing const_shared_lock_ptr) but I already created an [alternative](https://github.com/mguludag/synchronized_value) for use different kind of lock types
