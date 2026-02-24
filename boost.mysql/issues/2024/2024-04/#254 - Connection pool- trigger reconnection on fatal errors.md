# #254 - Connection pool: trigger reconnection on fatal errors [Open]

> Username: anarthal  
> Created at: 2024-04-29 16:59:48 UTC  
> Updated at: 2024-04-29 16:59:48 UTC  
> Url: https://github.com/boostorg/mysql/issues/254  

If a pooled connection encounters a fatal error (as per #253), we should trigger a re-connection. Currently:  
* If the connection gets reset (the default), the error gets detected and re-connection triggers - but we're wasting a server round-trip.  
* If the connection doesn't get reset (as per `return_without_reset`), the connection will never be reconnected, which can be problematic.
