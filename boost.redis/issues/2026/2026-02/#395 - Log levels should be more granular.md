# #395 - Log levels should be more granular [Open]

> Username: rmerrill-fw  
> Created at: 2026-02-15 17:40:32 UTC  
> Updated at: 2026-02-16 09:24:51 UTC  
> Url: https://github.com/boostorg/redis/issues/395  

The majority of log messages are at level `info`. If I set the log level to `notice` I get no log messages at all, but if I set it to `info` I get a log message for every single redis transaction.  
  
I really just want to log significant events like disconnect and reconnect attempts.

---

## Comment 1

> Username: anarthal  
> Created at: 2026-02-16 09:24:51 UTC  
> Url: https://github.com/boostorg/redis/issues/395#issuecomment-3907342249  

Hi! This is actually fixed in the upcoming 1.91 release. We're now using  
  
* Error when there is a communication error with the server, like a connection establishment error or a ping timeout.  
* Info for successful connection attempts. This is specially useful if you work with Sentinel, aa it logs the details of the concrete instance that oa being contacted.  
* Debug for everything else. In particular, the "xyz bytes written" message now uses debug.  
  
PR to look at is #389.  
  
Let me know if it looks good to you.  
  
Cheers,  
Rubén.
