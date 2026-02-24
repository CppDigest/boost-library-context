# #456 - Restart parser on callback error [Closed]

> Username: vinniefalco  
> Created at: 2017-06-09 22:02:52 UTC  
> Updated at: 2017-09-01 03:15:37 UTC  
> Closed at: 2017-09-01 03:15:37 UTC  
> Url: https://github.com/boostorg/beast/issues/456  

When the parser receives an error code from a callback, it should make sure that the parse can be re-attempted (i.e. be idempotent).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-01 03:15:37 UTC  
> Url: https://github.com/boostorg/beast/issues/456#issuecomment-326476856  

I think it works this way now
