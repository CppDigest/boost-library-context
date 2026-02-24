# #6 Distinguish between error statuses. [Merged]

> Username: danieljames  
> Created at: 2016-07-29 12:28:13 UTC  
> Updated at: 2016-08-05 10:41:27 UTC  
> Merged at: 2016-08-05 10:41:27 UTC  
> Closed at: 2016-08-05 10:41:27 UTC  
> Url: https://github.com/boostorg/inspect/pull/6  

Currently running inspect always returns an error status which means I can't tell when it's failed because there was a problem running it, or because a check failed. So return 2 when there was a problem running it (just for invalid command line options currently).

---
