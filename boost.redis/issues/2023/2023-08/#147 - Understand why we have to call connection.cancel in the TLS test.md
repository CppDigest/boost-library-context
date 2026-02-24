# #147 - Understand why we have to call connection.cancel in the TLS test [Closed]

> Username: mzimbres  
> Created at: 2023-08-26 13:50:35 UTC  
> Updated at: 2025-10-17 14:39:44 UTC  
> Closed at: 2025-10-17 14:39:44 UTC  
> Url: https://github.com/boostorg/redis/issues/147  

Since reconnection has been disabled and the acl prohibits selecting a database index, we should not have to call `cancel` on `async_exec` completion.

---

## Comment 1

> Username: anarthal  
> Created at: 2025-10-17 14:39:44 UTC  
> Url: https://github.com/boostorg/redis/issues/147#issuecomment-3415876826  

I don't think we have this test/situation anymore, so I'll be closing this.
