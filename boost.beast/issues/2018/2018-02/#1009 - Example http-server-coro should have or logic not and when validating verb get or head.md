# #1009 - Example http-server-coro should have or logic not and when validating verb get or head [Closed]

> Username: jdmairs  
> Created at: 2018-02-06 22:01:16 UTC  
> Updated at: 2018-02-06 22:07:58 UTC  
> Closed at: 2018-02-06 22:07:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1009  

https://github.com/boostorg/beast/blob/908f84cb3c023ed7c5bd19e2e5283d3218a6d820/example/http/server/coro/http_server_coro.cpp#L149

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-02-06 22:06:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1009#issuecomment-363581060  

You think it should read this way?  
```  
    if( req.method() != http::verb::get ||  
        req.method() != http::verb::head)  
```  
won't that always be `true`?

---

## Comment 2

> Username: jdmairs  
> Created at: 2018-02-06 22:07:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1009#issuecomment-363581344  

Sorry that's what I thought problem was!!!  Just discovered in debugger I was wrong.  Great stuff here.
