# #1029 - Tidy up teardown_tcp_op [Closed]

> Username: vinniefalco  
> Created at: 2018-02-18 17:24:00 UTC  
> Updated at: 2018-02-21 16:33:53 UTC  
> Closed at: 2018-02-21 16:33:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1029  

This composed operation should use a stackless coroutine, and save/restore the setting of `non_blocking`
